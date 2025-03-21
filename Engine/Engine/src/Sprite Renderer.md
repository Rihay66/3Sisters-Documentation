The Sprite Renderer is responsible for rendering Quads, and Lines given raw data. It provides multiples ways to render Quads, Lines, or Rectangles either by rendering each individually or all at the same time. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

The types of things that the Sprite Renderer can render which are Quads and Lines are considered and can be called "render primitives" and below are examples of what they looks like:

Quad:
![[quad.png]]
Line:
![[line.png]]
(Additionally) Quad Wireframe:
![[quadwire.png]]

Refer to GLM documentation: 
* http://glm.g-truc.net/0.9.9/api/modules.html

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

### Sprite Renderer is a Static Singleton Class

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

The Sprite Renderer does require a shader to be loaded and contain the following layout variables for the vertex [[Shader]] and fragment [[Shader]] (if it needs it) for each render primitive:

Quad:
```glsl
// Vertex
layout (location = 0) in vec2 vertex; 
layout (location = 1) in vec2 texCoords; 
layout (location = 2) in float texIndex;
layout (location = 3) in vec4 color;

out vec2 o_TexCoords;
out float o_TexIndex;
out vec4 o_spriteColor;

uniform mat4 projectionView;

// Fragment
in vec2 o_TexCoords;
in vec4 o_spriteColor;
in float o_TexIndex;

out vec4 color;

uniform sampler2D image[32];
```
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

Before using the Sprite Renderer it needs to be initialized and given a shader so the class contains a "Init()", in the example below we'll use [[Resource Manager]] to load a shader, use [[Camera]] to provide "projectionView" calculation and then initialize the Sprite Renderer
```cpp
void func(){
	// load the QUAD shader using Resource Manager
	ResourceManager::LoadShader("quad.vs", "quad.frag", nullptr, "quad");

	// get the QUAD shader
	Shader& quad = ResouceManager::GetShader("quad");

	// apply projection calculation to the Quad Shader using Camera
	Camera cam;
	cam.setDimensions(1280, 720);
	cam.calculateProjectionView(quad);

	// initialize Sprite Rendeder for Quad rendering
	SpriteRenderer::InitQuad(quad, {10.0f, 10.0f});
}
```

Within the Sprite Renderer it is flexible on which render primitive you want to use or want to enable later in your application

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

Because of the fragments variable that the Sprite Renderer interacts with 
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

However, all of this texture binding is already done through [[Resource Manager]] so the explanation given above is only useful when making your own solution

Header location/class name:
```cpp
#include <engine/sprite_renderer.hpp

class SpriteRenderer{
...
};
```
## Class Functions:
#### static public: InitQuad([[Shader]]&, glm::uvec2)
* used to initialize quad rendering and the pixel size of all quad object
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load QUAD shader
	// calculate projection and apply to QUAD shader
	// initialize Sprite Renderer for quad rendering
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}
```
#### static public: InitLine([[Shader]]&, glm::uvec2)
* used to initialize line rendering and the pixel size of all line objects, the pixel size also affects quad wireframe objects
* the width of lines by default is set to be 1, can be changed later however does also affects quad wireframe objects and all lines being rendered
* also when initializing the line renderer it allows for rendering quad wireframe objects
* When calling this function again, it will give a warning that it has been already initialized
```cpp
void func(){
	// load LINE shader
	// calculate projection and apply to LINE shader
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
	// calculate projection and apply to QUAD and LINE shader
	// initialize Sprite Renderer
	SpriteRenderer::Init(quadShader, lineShader, {10.0f, 10.0f}, {5.0f, 5.0f});
}
```
#### static public: DrawQuad(int, glm::vec2, glm::vec2, float, glm::vec4, const std::array<glm::vec2, 4>, const glm::vec4[])
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
	// calculate projection and apply to QUAD shader
	// initialize quad rendering
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render a quad
	int texIndex = ResourceManager::GetTextureIndex("example")
	SpriteRenderer::DrawQuad(texIndex, pos, size, rotation);
}
```
#### static public: DrawQuad(int, [[Interpolation]], glm::vec2, float, double, glm::vec4, std::array<glm::vec2,4>, glm::vec4[])
* draw a singular quad utilizing raw data, with interpolation
* the raw data consists of the following:
	* texture index <- found through [[Resource Manager]]
	* [[Interpolation]] <- tracks previous and current position of a quad to provide smooth transition, due remember to update the actual position of the object
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
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render a quad

	// get texture index from a loaded texture
	int texIndex = ResourceManager::GetTextureIndex("example");

	// draw quad
	SpriteRenderer::DrawQuad(texIndex, interpolation, size, rotation, alpha);
}
```
#### public static: DrawLine(glm::vec2, glm::vec2, glm::vec4)
* draw a single line from two given points
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
	SpriteRenderer::InitLine(lineShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render a line
	SpriteRenderer::DrawLine({0.0f, 0.0f}, {5.0f, 5.0f});
}
```
#### static public: DrawQuadWire(glm::vec2, glm::vec2, float, glm::vec4)
* draw a single wireframe of a quad utilizing raw data and lines
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
	SpriteRenderer::InitLine(lineShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render a line
	SpriteRenderer::DrawQuadWireFrame(position, size, rotation);
}
```
#### static public: StackQuad(int, glm::vec2, glm::vec2, float, glm::vec4, const std::array<glm::vec2, 4>, const glm::vec4[])
* store a single quad utilizing given raw data, without interpolation
* after calling this function it is REQUIRED to call FlushQuad() in order to render what was stored
* without the FlushQuad() stacked objects will be rendered either way, however the behavior is undefined and could lead to issues or errors
* the raw data consists of the following:
	* texture index <- found through [[Resource Manager]]
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
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render a quad
	
	// get texture index from a loaded texture
	int texIndex = ResourceManager::GetTextureIndex("example")
	
	// stack multiple quads
	SpriteRenderer::StackQuad(texIndex, position, size, rotation);
	SpriteRenderer::StackQuad(texIndex, position, size, rotation);

	// render all quads stacked
	SpriteRenderer::FlushQuads();
}
```
#### static public: StackQuad(int, [[Interpolation]], glm::vec2, float, double, glm::vec4, std::array<glm::vec2,4>, glm::vec4[])
* store a singular quad utilizing raw data, with interpolation
* after calling this function it is REQUIRED to call FlushQuad() in order to render what was stored
* without the FlushQuad() stacked objects will be rendered either way, however the behavior is undefined and could lead to issues or errors
* the raw data consists of the following:
	* texture index <- found through [[Resource Manager]]
	* [[Interpolation]] <- tracks previous and current position of a quad to provide smooth transition, due remember to update the actual position of the object
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
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render a quad
	
	// get texture index from a loaded texture
	int texIndex = ResourceManager::GetTextureIndex("example")
	
	// stack multiple quads with interpolation
	SpriteRenderer::StackQuad(texIndex, interpolation, size, rotation, alpha);
	SpriteRenderer::StackQuad(texIndex, interpolation, size, rotation, alpha);

	// render all quads stacked
	SpriteRenderer::FlushQuads();
}
```
#### public static: StackLine(glm::vec2, glm::vec2, glm::vec4)
* draw a single line from two given points
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
	SpriteRenderer::InitLine(lineShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render lines

	// stack multiple lines
	SpriteRenderer::StackLine({0.0f, 0.0f}, {5.0f, 5.0f});
	SpriteRenderer::StackLine({2.0f, 2.0f}, {7.0f, 7.0f});

	//render all lines stacked
	SpriteRenderer::FlushLines();
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
	SpriteRenderer::InitQuad(quadShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render quads
	
	// stack multiple quads
	...
	// render all quads stacked
	SpriteRenderer::FlushQuads();
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
	SpriteRenderer::InitLine(lineShader, {10.0f, 10.0f});
}

...

void render(double alpha){
	// render lines
	
	// stack multiple lines
	...
	// render all lines stacked
	SpriteRenderer::FlushLines();
}
```
#### public static: SetLineWidth(float)
* set the width of all lines being rendered
* cannot be used to individually set the width of lines
* doesn't require for line rendering to be initialized
```cpp
void func(){
	// set a line width for all lines
	SpriteRenderer::SetLineWidth(2.0f);
}
```
#### public static: SetQuadPixelSize(glm::uvec2)
* set the model pixel size of quads
* this affects the default by pixel size of all quads
* doesn't require for quad rendering to be initialized
```cpp
void func(){
	// set a pixel size for all quads
	SpriteRenderer::SetQuadPixelSize({60.0f, 60.0f});
}
```
#### public static: SetLinePixelSize(glm::uvec2)
* set the model pixel size of lines
* this affects the default by pixel size of all lines
* doesn't require for line rendering to be initialized
```cpp
void func(){
	// set a pixel size for all lines
	SpriteRenderer::SetQuadPixelSize({60.0f, 60.0f});
}
```
#### public static: GetLineWidth() -> returns float
* get the current width of all lines
* doesn't require for line rendering to be initialized
```cpp
void func(){
	// get the width of all lines
	float width = SpriteRenderer::GetLineWidth();
}
```