The Line Renderer is responsible for rendering Lines given raw data. It provides multiples ways to render Lines or Rectangles either by rendering each individually or all at the same time. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

The types of things that the Line Renderer can render which are Lines are considered and can be called "render primitives" and below are examples of what they looks like:

Line:
![[line.png]]
Rectangle or Quad Wireframe:
![[quadwire.png]]

Refer to GLM documentation: 
* http://glm.g-truc.net/0.9.9/api/modules.html

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

### Line Renderer is a Static Singleton Class

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included

For example: including a Static Singleton Class like [[Texture Manager]]

``` cpp
#include <ResourceSystems/Managers/ResourceManager.hpp>
```

From this point the class [[Texture Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <resourcesystems/managers/resource_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	TextureManager::BindTextures();
}
```

The Line Renderer does require a [[Shader]] to be loaded and contain the following layout variables for the vertex [[Shader]] and fragment [[Shader]] for each render primitive:

Line:
```glsl
// Vertex
layout (location = 0) in vec2 vertex; 
layout (location = 1) in vec4 color;

out vec4 o_lineColor;

uniform mat4 projectionView;

// Fragment
in vec4 o_lineColor;

out vec4 color;

```

Before using the Line Renderer it needs to be initialized and given a [[Shader]] so the class contains a "Init()", in the example below we'll use [[Shader Manager]] to load a [[Shader]], use [[Camera]] to provide "projectionView" calculation and then initialize the Line Renderer
```cpp
void func(){
	// load the LINE shader using Shader Manager
	ShaderManager::LoadShader("line.vert", "line.frag", nullptr, "line");

	// get the LINE shader
	Shader& line = ShaderManager::GetShader("line");

	// apply projection calculation to the Line Shader using Camera
	Camera cam;
	cam.setDimensions(1280, 720);
	cam.calculateProjectionView(line);

	// initialize Line Rendeder for Line rendering
	LineRenderer::Init(line);
}
```

When it comes to rendering you MUST call certain function pertaining to both OpenGL and whichever [[Window]] library you pick like SDL or GLFW or custom
```cpp
void func(){
	// clear screen
	glClear(GL_COLOR_BUFFER_BIT);

	// render something, example: using Line Renderer

	// example: swap window buffers using GLFW
	glfwSwapBuffers(windowHandle);	
}
```
Fortunately, the already build window classes, [[Window-GLFW]] and [[Window-SDL]], can already do this for you and they provide a paradigm which you can find in [[Window]] where to do all your rendering

Header location/class name:
```cpp
#include <engine/line_renderer.hpp

class LineRenderer{
...
};
```
## Class Functions:
#### static public: InitLine([[Shader]]&)
* used to initialize line rendering and set a shader
* the width of lines by default is set to be 1, can be changed later however does also affects quad wireframe objects and all lines being rendered
* also when initializing the line renderer it allows for rendering rectangles or otherwise known as quad wireframe objects
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load LINE shader
	// calculate projection and apply to LINE shader
	// initialize Line Renderer for line rendering
	LineRenderer::InitLine(lineShader);
}
```

#### public static: DrawLine(glm::vec2, glm::vec2, glm::vec4)
* draw a single line using two given points
* the raw data consists of the following:
	* start position
	* end position
	* color <- all RGBA values set to 1
* the width of lines by default is set to be 1, can be changed later however does also affects quad wireframe objects and all lines being rendered
* due note that line rendering must be initialized otherwise you will be given an error message
```cpp
// using window paradigm
void init(){
	// load LINE shader
	// calculate projection and apply to LINE shader
	// initialize line rendering
	LineRenderer::InitLine(lineShader);
}

...

void render(double alpha){
	// render a line
	LineRenderer::DrawLine({0.0f, 0.0f}, {5.0f, 5.0f});
}
```
#### static public: DrawQuadWire(glm::vec2, glm::vec2, float, glm::vec4)
* draw a single wireframe of a quad utilizing lines
* the data consists of the following:
	* position
	* size
	* rotation
	* color <- all RGBA values set to 1
* the width of lines by default is set to be 1, can be changed later however does also affects quad wireframe objects and all lines being rendered
* due note that line rendering must be initialized otherwise you will be given an error message
```cpp
// using window paradigm
void init(){
	// load LINE shader
	// calculate projection and apply to LINE shader
	// initialize line rendering
	LineRenderer::InitLine(lineShader);
}

...

void render(double alpha){
	// render a line
	LineRenderer::DrawQuadWireFrame(position, size, rotation);
}
```
#### public static: StackLine(glm::vec2, glm::vec2, glm::vec4)
* stack a single line using two given points
* after calling this function it is REQUIRED to call FlushLines() in order to render what was stored
* without the FlushLines() stacked objects will be rendered either way, however the behavior is undefined and could lead to issues or errors
* the raw data consists of the following:
	* start position
	* end position
	* color <- all RGBA values set to 1
* the width of lines by default is set to be 1, can be changed later however does also affects quad wireframe objects and all lines being rendered
* due note that line rendering must be initialized otherwise you will be given an error message
* it is faster to stack multiple lines than doing a singular draw for each line as it utilizes the batch rendering technique
```cpp
// using window paradigm
void init(){
	// load LINE shader
	// calculate projection and apply to LINE shader
	// initialize line rendering
	LineRenderer::InitLine(lineShader);
}

...

void render(double alpha){
	// render lines

	// stack multiple lines
	LineRenderer::StackLine({0.0f, 0.0f}, {5.0f, 5.0f});
	LineRenderer::StackLine({2.0f, 2.0f}, {7.0f, 7.0f});

	//render all lines stacked
	LineRenderer::FlushLines();
}
```
#### public static: FlushLines()
* used to tell to the GPU to render the stored lines in the buffer
* this causes a single draw call request to the GPU
* it is REQUIRED to call this function when stacking lines
```cpp
// using window paradigm
void init(){
	// load a texture
	// load LINE shader
	// calculate projection and apply to LINE shader
	// initialize line rendering
	LineRenderer::InitLine(lineShader);
}

...

void render(double alpha){
	// render lines
	
	// stack multiple lines
	...
	// render all lines stacked
	LineRenderer::FlushLines();
}
```
#### public static: SetLineWidth(float)
* set the width of all lines being rendered
* cannot be used to individually set the width of lines
* doesn't require for line rendering to be initialized
```cpp
void func(){
	// set a line width for all lines
	LineRenderer::SetLineWidth(2.0f);
}
```
#### public static: GetLineWidth() -> returns float
* get the current width of all lines
* doesn't require for line rendering to be initialized
```cpp
void func(){
	// get the width of all lines
	float width = LineRenderer::GetLineWidth();
}
```