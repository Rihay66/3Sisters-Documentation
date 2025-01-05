The Engine modules are responsible for handling the heavy lifting such as Physics and Rendering, and others. Also, note that some modules don't work properly without a generated window handle context like the Renderer Modules.

These Engine modules are all Static Singleton Classes where their resources are stored statically and automatically freed without any explicit function call. 

To include a Static Singleton module in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included.

For example: including a Static Singleton module like [[Resource Manager]]

``` cpp
#include <ResourceSystems/Managers/ResourceManager.hpp>
```

From this point the class [[Resource Manager]] calls its private constructor which only initializes as static object with its private static variables.

In C++ to use a function of a Static Singleton class can be done as follow:

```cpp
#include <resourcesystems/managers/resource_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	// StaticClassName::Function()
	ResourceManager::BindTextures();
}
```

However, some of the Static Singleton Classes require more information to be fully used anywhere else so some may need to be used in sequence.

For example: [[Sprite Renderer]] requires a loaded [[Shader]] and optionally a set universal sprite size for any rendered quad sprite (also indirectly the [[Shader]]'s projectionView must be set utilizing the [[Camera]] module)
``` cpp
#include <engine/sprite_renderer.hpp>

void func(){
	//... load a shader
	//... set up camera
	// initialize sprite renderer with a shader and a custom 2D sprite size 
	SpriteRenderer::Init(shader, {15.0f, 60.0f});
}
```

Then the [[Sprite Renderer]] is now able to render (Also note that to render you must use the [[Window]] function paradigm if you are utilizing the engine's [[Window]] module)
```cpp
#include <engine/sprite_renderer.hpp>

//* utilize the Window paradigm 

void init(){
	// load a shader
	// load a texture
	// set up camera
	// initialie sprite renderer with a shader and utilize default 2D sprite size
}

//...

void render(double alpha){
	// render a quad on the screen
	SpriteRenderer::DrawQuad(textureIndex, position, size, rotation, color);	
}

```

Here are all the Engine modules that handle a specific functionality:
[[Sprite Renderer]] - for handling quad, line, rectangle rendering
[[Text Renderer]] - for handling text rendering

Here are all the Engine components/types:
[[Interpolation]]