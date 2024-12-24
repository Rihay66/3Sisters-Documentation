The Sprite Renderer is responsible for rendering Quads, and Lines given raw data. It provides multiples ways to render Quads, Lines, or Rectangles either by rendering each individually or all at the same time. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

The types of things that the Sprite Renderer can render which are Quads and Lines are considered and can be called "render primatives" and below are examples of what they looks like:

Quad:
![[quad.png]]
Line:
![[line.png]]
(Additionally pre-built) Quad Wireframe:
![[quadwire.png]]

Refer to GLM documentation: 
* http://glm.g-truc.net/0.9.9/api/modules.html
Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page
Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

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

The Sprite Renderer does require a shader to be loaded and contain the following layout variables for the vertex [[Shader]] and fragment [[Shader]] (if it needs it) for each render primative:

Quad:
```glsl
// Vertex
layout (location = 0) in vec2 vertex; 
layout (location = 1) in vec2 texCoords; 
layout (location = 2) in float texIndex;
layout (location = 3) in vec4 color;
// Fragment
uniform sampler2D image[32];
```
Line:
```glsl
// Vertex
layout (location = 0) in vec2 vertex; 
layout (location = 1) in vec4 color;
```

Before using the Sprite Renderer it needs to be initialized and given a shader so the class contains a "Init()", in the example below we'll use [[Resource Manager]] to load a shader and then initialize Sprite Renderer
```cpp
void func(){
	// load the QUAD shader using Resource Manager
	ResourceManager::LoadShader("quad.vs", "quad.frag", nullptr, "quad");

	// get the QUAD shader
	Shader& quad = ResouceManager::GetShader("quad");

	// initialize Sprite Rendeder for Quad rendering
	SpriteRenderer::InitQuad(quad, {10.0f, 10.0f});
}
```

Within the Sprite Renderer it is flexible on which render primative you want to use or want to enable later in your application

When it comes to rendering you MUST call certain function pertaining to both OpenGL and whichever [[Window]] library you pick like SDL or GLFW or custom
```cpp
void func(){
	// clear screen
	glClear(GL_COLOR_BUFFER_BIT);

	// render something using Sprite Renderer

	// example: swap window buffers using GLFW
	glfwSwapBuffers(windowHandle);	
}
```
Fortunately, the already build window classes, [[Window-GLFW]] and [[Window-SDL]], can already do this for you and they provide a paradigm which you can find in [[Window]] when you do all your rendering

(Talk about when it comes to textures)
Because of the fragments variable that the Sprite Renderer interacts with 
```glsl
```

Header location/class name:
```cpp
#include <engine/sprite_renderer.hpp

class SpriteRenderer{
...
};
```
## Class Functions:
#### static public: InitQuad([[Shader]]&, glm::uvec2)
* used to initialize quad rendering and the pixel size of all quad objects
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load QUAD shader
	// initialize Sprite Renderer for quad rendering
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}
```
#### static public: InitLine([[Shader]]&, glm::uvec2)
* used to initialize line rendering and the pixel size of all line objects, the pixel size also affects quad wireframe objects
* the width of lines by default is set to be 1, can be changed later, does also affects quad wireframe objects
* also when initializing the line renderer it allows for rendering quad wireframe objects
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load LINE shader
	// initialize Sprite Renderer for line rendering
	SpriteRenderer::InitLine(lineShader, {5.0f, 5.0f});
}
```
#### static public: Init([[Shader]]&, [[Shader]]&, glm::uvec2, glm::uvec2)
* used to initialize both quad and line renderer and their respective pixel size of all quad and all line objects (the pixel size given for line rendering affects quad wireframe objects)
* the width of lines by default is set to be 1, can be changed later, does also affects quad wireframe objects
* also when initializing the line renderer it allows for rendering quad wireframe objects
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load QUAD and LINE shaders
	// initiialize Sprite Renderer
	SpriteRenderer::Init(quadShader, lineShader, {10.0f, 10.0f}, {5.0f, 5.0f});
}
```
#### static public: DrawQuad(int, glm::vec2, glm::vec2, float, glm::vec4, const std::array<glm::vec2, 4>, const glm::vec4)
* draw a singular quad utilizing given raw data, without interpolation
* the raw data consists of the following:
	* texture index <- found through [[Resource Manager]]
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
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render a quad
	int texIndex = ResourceManager::GetTextureIndex("example")
	SpriteRenderer::DrawQuad(texIndex, pos, size, rotation);
}
```