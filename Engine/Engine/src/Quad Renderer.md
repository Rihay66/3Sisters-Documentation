The Quad Renderer is responsible for rendering Quads raw data. It provides multiples ways to render Quads either by rendering each individually or all at the same time. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

The types of things that the Quad Renderer can render which are Quads are considered and can be called "render primitives" and below are examples of what they looks like:

Quad:
![[quad.png]]

Refer to GLM documentation: 
* http://glm.g-truc.net/0.9.9/api/modules.html

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

### Quad Renderer is a Static Singleton Class

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

The Quad Renderer does require a [[Shader]] to be loaded and contain the following layout variables for the vertex [[Shader]] and fragment [[Shader]] (if it needs it) for each render primitive:

Quad:
```glsl
// Vertex
layout (location = 0) in vec2 vertex; 
layout (location = 1) in vec2 texCoords; 
layout (location = 2) in float texIndex;
layout (location = 3) in vec4 color;

out vec2 o_TexCoords;
out float o_TexIndex;
out vec4 o_quadColor;

uniform mat4 projectionView;

// Fragment
in vec2 o_TexCoords;
in vec4 o_quadColor;
in float o_TexIndex;

out vec4 color;

uniform sampler2D image[32];
```

Before using the Quad Renderer it needs to be initialized and given a shader so the class contains a "Init()", in the example below we'll use [[Shader Manager]] to load a shader, use [[Camera]] to provide "projectionView" calculation and then initialize the Quad Renderer
```cpp
void func(){
	// load the QUAD shader using Resource Manager
	ShaderManager::LoadShader("quad.vert", "quad.frag", nullptr, "quad");

	// get the QUAD shader
	Shader& quad = ShaderManager::GetShader("quad");

	// apply projection calculation to the Quad Shader using Camera
	Camera cam;
	cam.setDimensions(1280, 720);
	cam.calculateProjectionView(quad);

	// initialize Quad Rendeder for Quad rendering
	QuadRenderer::InitQuad(quad;
}
```

Within the Quad Renderer it is flexible on which render primitive you want to use or want to enable later in your application

When it comes to rendering you MUST call certain function pertaining to both OpenGL and whichever [[Window]] library you pick like SDL or GLFW or custom
```cpp
void func(){
	// clear screen
	glClear(GL_COLOR_BUFFER_BIT);

	// render something, example: using Quad Renderer

	// example: swap window buffers using GLFW
	glfwSwapBuffers(windowHandle);	
}
```
Fortunately, the already build window classes, [[Window-GLFW]] and [[Window-SDL]], can already do this for you and they provide a paradigm which you can find in [[Window]] where to do all your rendering

Because of the fragments variable that the Quad Renderer interacts with 
```glsl
// Fragment
uniform sampler2D image[32];
```
It requires textures that are loaded to be bounded to an index from 0-31. The variable mentioned in the fragment shader must be initialized
```cpp
void func(){
	// grab the uniform location of 'image' in the shader, the name 'image' is explicit
	auto loc = glGetUniformLocation(quadShader.getID(), "image");

	// set up array to the size of the max number of textures
	int samplers[maxTextureSlots];
	
	// set up samplers array
	for (int i = 0; i < maxTextureSlots; i++){
		samplers[i] = i;
	}
}
```
To bind each loaded texture to an index from 0-31, due note that the engine by default uses OpenGL 4.5 so here how it's done
```cpp
void func(){
	// texIDList contains a list of unique ID from loaded textures

	// bind all the textures from first to last
	for(int i = 0; i < texIDList.size(); i++){
		// call to bind texture by their ID to an index
		glBindTextureUnit(i, texIDList[i]);
	}
}
```
Using a texture depending on how you organized your textures you should utilize the index and not the texture ID to apply a texture to whatever you are rendering.

However, all of this texture binding is already done through [[Texture Manager]] so the explanation given above is only useful when making your own solution

Header location/class name:
```cpp
#include <engine/quad_renderer.hpp

class QuadRenderer{
...
};
```
## Class Functions:
#### static public: Init([[Shader]]&)
* used to initialize quad rendering and set the shader
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load QUAD shader
	// calculate projection and apply to QUAD shader
	// initialize Quad Renderer for quad rendering
	QuadRenderer::InitQuad(quadShader);
}
```
#### static public: DrawQuad(int, glm::vec2, glm::vec2, float, glm::vec4, const std::array<glm::vec2, 4>, const glm::vec4[])
* draw a singular quad utilizing given raw data
* the raw data consists of the following:
	* texture index <- found through [[Texture Manager]]
	* position
	* size
	* rotation
	* color <- all RGBA values set to 1
	* texture coordinates <- Optional
	* vertex coordinates <- Optional
* due note that quad rendering must be initialized otherwise you will be given an error message
```cpp
// using window paradigm
void init(){
	// load a texture
	// load QUAD shader
	// calculate projection and apply to QUAD shader
	// initialize quad rendering
	Quadenderer::InitQuad(quadShader);
}

...

void render(double alpha){
	// render a quad
	int texIndex = TextureManager::GetTextureIndex("example")
	QuadRenderer::DrawQuad(texIndex, pos, size, rotation);
}
```
#### static public: StackQuad(int, glm::vec2, glm::vec2, float, glm::vec4, const std::array<glm::vec2, 4>, const glm::vec4[])
* store a single quad utilizing given raw data
* after calling this function it is REQUIRED to call FlushQuad() in order to render what was stored
* without the FlushQuad() stacked objects will be rendered either way, however the behavior is undefined and could lead to issues or errors
* the raw data consists of the following:
	* texture index <- found through [[Texture Manager]]
	* position
	* size
	* rotation
	* color <- all RGBA values set to 1
	* texture coordinates <- Optional
	* vertex coordinates <- Optional
* due note that quad rendering must be initialized otherwise you will be given an error message
* it is faster to stack multiple quads than doing a singular draw for each quad as it utilizes the batch rendering technique
```cpp
// using window paradigm
void init(){
	// load a texture
	// load QUAD shader
	// calculate projection and apply to QUAD shader
	// initialize quad rendering
	QuadRenderer::InitQuad(quadShader);
}

...

void render(double alpha){
	// render a quad
	
	// get texture index from a loaded texture
	int texIndex = TextureManager::GetTextureIndex("example")
	
	// stack multiple quads
	QuadRenderer::StackQuad(texIndex, position, size, rotation);
	QuadRenderer::StackQuad(texIndex, position, size, rotation);

	// render all quads stacked
	QuadRenderer::FlushQuads();
}
```
#### public static: FlushQuads()
* used to tell to the GPU to render the stored quads in the buffer
* this causes a single draw call request to the GPU
* it is REQUIRED to call this function when stacking quads
```cpp
// using window paradigm
void init(){
	// load a texture
	// load QUAD shader
	// calculate projection and apply to QUAD shader
	// initialize quad rendering
	QuadRenderer::InitQuad(quadShader);
}

...

void render(double alpha){
	// render quads
	
	// stack multiple quads
	...
	// render all quads stacked
	QuadRenderer::FlushQuads();
}
```
