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

For example: including a Static Singleton Class like [[Resource Manager]]

``` cpp
#include <ResourceSystems/Managers/ResourceManager.hpp>
```

From this point the class [[Resource Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <resourcesystems/managers/resource_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	ResourceManager::BindTextures();
}
```
The Text Renderer does require a shader to be loaded and contain the following layout variables for the vertex [[Shader]] and fragment [[Shader]] (if it needs it) for rendering text:

Text:
```glsl
// Vertex
layout (location = 0) in vec4 vertex;
out vec2 o_TexCoords;
uniform mat4 projection;

// Fragment
in vec2 o_TexCoords;
out vec4 color;
uniform vec4 textColor;
uniform sampler2D text;
```

Before using the Text Renderer it needs to be initialized and given a shader so the class contains a "Init()", in the example below we'll use [[Resource Manager]] to load a shader and then initialize the Text Renderer
```cpp
void func(){
	// load the TEXT shader using Resource Manager
	ResourceManager::LoadShader("text.vs", "text.frag", nullptr, "text");

	// get the TEXT shader
	Shader& text = ResouceManager::GetShader("text");

	// initialize Text Rendeder for Text rendering
	TextRenderer::Init(1280, 720, text);
}
```

When it comes to rendering you MUST call certain function pertaining to both OpenGL and whichever [[Window]] library you pick like SDL or GLFW or custom
```cpp
void func(){
	// clear screen
	glClear(GL_COLOR_BUFFER_BIT);

	// render something, example: using Sprite Renderer

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
#### static public: Init(unsigned int, unsigned int, [[Shader]]&)
* used to initialize text rendering and projection of all text
* all text are positioned per pixel 
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load TEXT shader
	// initialize Text Renderer for text rendering
	TextRenderer::Init(1280, 720, textShader);
}
```
#### static public: DrawText(std::map<char, Character>&, std::string, glm::vec2, glm::vec2, glm::vec4)
* used to draw text on the screen using a loaded font
* the raw data consists of the following:
	* chars <- found through [[Resource Manager]]
	* position
	* size
	* color <- all RGBA values set to 1
* recommended to use a font loaded through from the [[ResourceManager]]
```cpp
// using window paradigm
void init(){
	// load TEXT shader
	// load font
	// initialize Text Renderer for text rendering
	TextRenderer::Init(1280, 720, textShader);
}

...

void render(double alpha){
	// get loaded font
	auto& font = ResourceManager::GetFontTexture("arial");

	// render text
	TextRenderer::DrawText(font, "example text", {300.0f, 400.0f}, {1.0f, 1.0f})
}
```