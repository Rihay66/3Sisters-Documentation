The Text Renderer is responsible for rendering text given a font and raw data. It provides for flexible output of different fonts. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

Refer to GLM documentation: 
* http://glm.g-truc.net/0.9.9/api/modules.html

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

### Text Renderer is a Static Singleton Class

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included

For example: including a Static Singleton Class like [[Texture Manager]]

``` cpp
#include <resourcesystems/managers/texture_manager.hpp>
```

From this point the class [[Texture Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <resourcesystems/managers/texture_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	TextureManager::BindTextures();
}
```

The Text Renderer does require a shader to be loaded and contain the following layout variables for the vertex [[Shader]] and fragment [[Shader]] for rendering text:

Text:
```glsl
// Vertex
layout (location = 0) in vec3 vertex; 
layout (location = 1) in vec2 texCoords; 
layout (location = 2) in vec4 color;

out vec2 o_TexCoords;
out vec4 o_color;

uniform mat4 projectionView;

// Fragment
in vec2 o_TexCoords;
in vec4 o_color;

out vec4 color;

layout (binding = 0) uniform sampler2D text;
```

Before using the Text Renderer it needs to be initialized and given a shader so the class contains a "Init()", in the example below we'll use [[Shader Manager]] to load a shader and [[Texture Manager]] to load the font, use [[Camera]] to provide "projectionView" calculation and then initialize the Text Renderer
```cpp
void func(){
	// load the TEXT shader using Shader Manager
	ShaderManager::LoadShader("text.vert", "text.frag", nullptr, "text");

	// load a font using the Texture Manager
	TextureManager::LoadFontTexture("fonts/Arial.tff", "font", 512, 512, 64.0f);

	// get the TEXT shader
	Shader& text = ShaderManager::GetShader("text");
		
	// apply projection calculation to the Line Shader using Camera
	Camera cam;
	cam.setDimensions(1280, 720);
	cam.calculateProjectionView(text);

	// initialize Text Rendeder for Text rendering
	TextRenderer::Init(text, current_window_height);
}
```

When it comes to rendering you MUST call certain function pertaining to both OpenGL and whichever [[Window]] library you pick like SDL or GLFW or custom
```cpp
void func(){
	// clear screen
	glClear(GL_COLOR_BUFFER_BIT);

	// render something, example: using Text Renderer

	// example: swap window buffers using GLFW
	glfwSwapBuffers(windowHandle);	
}
```
Fortunately, the already build window classes, [[Window-GLFW]] and [[Window-SDL]], can already do this for you and they provide a paradigm which you can find in [[Window]] where to do all your rendering

Header location/class name:
```cpp
#include <engine/text_renderer.hpp>

class TextRenderer{
...
};
```
## Class Functions:
#### static public: Init([[Shader]]&, unsigned int)
* used to initialize text rendering of all text
*  requires the height of the display area which is used to calculate the pixel scale of the rendered text
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load TEXT shader
	// load a font texture
	// calculate projection and apply to TEXT shader
	// initialize Text Renderer for text rendering
	TextRenderer::Init(textShader, currrent_window_height);
}
```
#### static public: DrawCharacters(CharacterSet&, std::string, glm::vec3, float, float, glm::vec4)
* used to draw text on the screen using a loaded font
* the raw data consists of the following:
	* CharacterSet <- found through [[Texture Manager]]
	* position
	* rotation
	* size
	* color <- all RGBA values set to 1 by default
* recommended to use a font loaded through from the [[Texture Manager]]
```cpp
// using window paradigm
void init(){
	// load TEXT shader
	// calculate projection and apply to TEXT shader
	// load font
	// initialize Text Renderer for text rendering
	TextRenderer::Init(textShader, window_height);
}

...

void render(double alpha){
	// get loaded font
	auto& font = TextureManager::GetFontTexture("arial");

	// render text
	TextRenderer::DrawCharacters(font, "example text", {0.5f, 0.0f, 0.0f}, 45.0f, 1.0f);
}
```
#### static public: StackCharacters(CharacterSet&, std::string, glm::vec3, float, float, glm::vec4)
* used to store text on the stack,can be called multiple times, which can be then be "Flushed" or renderer on the screen using a loaded font
* after calling this function it is REQUIRED to call FlushText() in order to render what was stored. Without the FlushText() stacked objects will be rendered either way, however the behavior is undefined and could lead to issues or errors
* NOTE: if wanting to use multiple fonts you need to stack and flush for each particular font being used, it's not possible to mix different fonts when stacking
* the raw data consists of the following:
	* CharacterSet <- found through [[Texture Manager]]
	* position
	* rotation
	* size
	* color <- all RGBA values set to 1 by default
* recommended to use a font loaded through from the [[Texture Manager]]
* due note that text rendering must be initialized otherwise you will be given an error message
* it is faster to stack multiple text than doing a singular draw for each text as it utilizes the batch rendering technique which allows to render all at once
```cpp
// using window paradigm
void init(){
	// load TEXT shader
	// calculate projection and apply to TEXT shader
	// load font 
	// initialize Text Renderer for text rendering
	TextRenderer::Init(textShader, window_height);
}

...

void render(double alpha){
	// get loaded font
	auto& font = TextureManager::GetFontTexture("arial");

	// stack text
	TextRenderer::StackCharacters(font, "example text", {0.5f, 0.0f, 0.0f}, 45.0f, {1.0f, 1.0f});
	TextRenderer::StackCharacters(font, "Hello World!", {-0.5f, 0.0f, 0.0f}, 0.0f, 0.8f);

	// draw text
	TextRenderer::FlushText();
}
```
#### static public: SetWindowHeight(unsigned int)
* set the window height, which is used to calculate the pixel scale of the rendered text
```cpp
void func(){
	// set the window height
	TextRenderer::SetWindowHeight(720);
}
```
#### static public: FlushText()
* used to tell the GPU to render the stored text in the stack buffer
* this causes a single draw call request to the GPU
* After the call of said function it calls BindTextures() from [[Texture Manager]]
* it is REQUIRED to call this function when stacking text
```cpp
// using window paradigm
void init(){
	// load TEXT shader
	// calculate projection and apply to TEXT shader
	// load font 
	// initialize Text Renderer for text rendering
	TextRenderer::Init(textShader, window_height);
}

...

void render(double alpha){
	// get loaded font
	auto& font = TextureManager::GetFontTexture("arial");

	// stack text
	TextRenderer::StackCharacters(font, "example text", {0.5f, 0.0f, 0.0f}, 45.0f, {1.0f, 1.0f});
	TextRenderer::StackCharacters(font, "Hello World!", {-0.5f, 0.0f, 0.0f}, 0.0f, 0.8f);

	// draw text
	TextRenderer::FlushText();
}
```