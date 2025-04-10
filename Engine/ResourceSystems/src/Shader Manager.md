The ShaderManager is responsible for handling and managing either loading or retrieving [[Shader]]s from a given file location. It provides the loaded resources to be available across the runtime of the application, hence this class is a Static Singleton Class. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

### Shader Manager is a Static Singleton Class

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included.

For example: including a Static Singleton Class like [[Texture Manager]]

``` cpp
#include <resourcesystems/managers/texture_manager.hpp>
```

From this point the class [[Texture Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object.

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <resourcesystems/managers/texture_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	TextureManager::BindTextures();
}
```

Within the shader manager it manages resources by having them be pointing to a given name and once set it then can be used for retrieving the desired resource. 

For Example: Load a texture using [[Texture Manager]] and give it a name
```cpp
void func(){
	// load and generate a texture from file and give it a name
	TexutreManager::LoadTexture("cat.png", "Pet");
}
```

From the example above, we loaded a texture called "cat.png" and gave it a name of "Pet", so within the manager it generated a texture that points to the name "Pet". 

For Example: Retrieve the generated texture using the given name
```cpp
void func(){
	// retrieve the texture index to apply to a quad
	int textureIndex = TextureManager::GetTextureIndex("Pet");
	
	// utilize retrieved texture for rendering...
}
```

Most managers that manage resources utilize this system of storing and retrieving stuff from just using a name and due note that name is explicit meaning that "Pet" != "pet".

Any resource loaded into a Static Singleton Class remains persistent until program exit, which means that resources loaded at the beginning of program initialization can be then accessed at any point in the program thereafter.

```cpp
// initialization function that is called at the start of the program
void init(){
	// load a texture into TextureManager
	TextureManager::LoadTexture("textures/example.png", "example");
}

...

// function used at some point in program
void func(){
	// get reference of loaded texture
	Texture& tex = TextureManager::GetTexture("example");
}
```

For Example: [[Texture Manager]] usage across different files
```cpp
// texture_getter.hpp

#include <resourcesystems/managers/texture_manager.hpp>

// declare and define function
void getTexture(){
	// retrieve a texture from TextureManager
	TextureManager::GetTexture("example");
}
```

```cpp
//main.cpp

#include <resourcesystems/managers/texture_manager.hpp>
#include "texture_getter.hpp"

int main(){
	// load a texture into TextureManager
	TextureManager::LoadTexture("textures/example.png", "example");
	
	// get the texture
	getTexture();
	
	// exit program
	return 0;
}
```

Obviously, due note that retrieving something that is being loaded later causes an error as what's being retrieved doesn't exist yet at the point where its being called

For example: Retrieve and then load a texture
```cpp
void func(){
	// retrieve a texture from TextureManager
	TextureManager::GetTexture("example"); //!ERROR: "example" doesn't exist
	
	// load a texture into TextureManager
	TextureManager::LoadTexture("textures/example.png", "example");
	
	// from this point and onwards "example" exists
}
```

Header location/namespace/class name
```cpp
#include <resourcesystems/managers/shader_manager.hpp>

class ShaderManager{
...
};
```
## Class Functions:
#### static public: LoadShader(const char*, const char*, const char* , std::string) -> returns [[Shader]]&
* loads (and generates) a shader program from file along with a name, loads the files in this order of vertex, fragment (and geometry) and extracts the shader's source code
* returns a reference of the loaded/generated shader for external use
* it is optional to load a geometry shader file, if there is no geometry shader then set as nullptr
```cpp
void func(){
	// load a shader
	ShaderManager::LoadShader("quad.vert","quad.frag", nullptr, "shader");
}
```
#### static public: GetShader(std::string) -> [[Shader]]&
* used to retrieve a stored shader
```cpp
void func(){
	// load a shader
	// get the shader
	Shader& shader = ShaderManager::GetShader("quad");
}
```