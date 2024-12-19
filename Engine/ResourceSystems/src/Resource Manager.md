The Resource Manager is responsible for handling and managing either loading or retrieving [[Shader]]s, [[Texture]]s, SubTextures, and Fonts from a given file location. It provides the loaded resources to be available across the runtime of the application, hence this class is a Static Singleton Class. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included.

For example: including a Static Singleton Class like [[Resource Manager]]

``` cpp
#include <ResourceSystems/Managers/ResourceManager.hpp>
```

From this point the class [[Resource Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object.

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <resourcesystems/managers/resource_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	ResourceManager::BindTextures();
}
```

Within the resource manager it manages resources by having them be pointing to a given name and once set it then can be used for retrieving the desired resource. 

For Example: Load a texture and give it a name
```cpp
void func(){
	// load and generate a texture from file and give it a name
	ResourceManager::LoadTexture("cat.png", "Pet");
}
```

From the example above, we loaded a texture called "cat.png" and gave it a name of "Pet", so within the manager it generated a texture that points to the name "Pet". 

For Example: Retrieve the generated texture using the given name
```cpp
void func(){
	// retrieve the texture index to apply to a quad
	int textureIndex = ResourceManager::GetTextureIndex("Pet");
	
	// utilize retrieved texture for rendering...
}
```

Most managers that manage resources utilize this system of storing and retrieving stuff from just using a name and due note that name is explicit meaning that "Pet" != "pet".

Any resource loaded into a Static Singleton Class remains persistent until program exit, which means that resources loaded at the beginning of program initialization can be then accessed at any point in the program thereafter.

```cpp
#include <resourcesystems/managers/resource_manager.hpp>

// initialization function that is called at the start of the program
void init(){
	// load a texture into ResourceManager
	ResourceManager::LoadTexture("textures/example.png", "example");
}

// function used at some point in program
void func(){
	// get loaded texture
	Texture tex& = ResourceManager::GetTexture("example");
}
```

For Example: Resource Manager usage across different files
```cpp
// texture_getter.hpp

#include <resourcesystems/managers/resource_manager.hpp>

// declare and define function
void getTexture(){
	// retrieve a texture from ResourceManager
	ResourceManager::GetTexture("example");
}
```

```cpp
//main.cpp

#include <resourcesystems/managers/resource_manager.hpp>
#include "texture_getter.hpp"

int main(){
	// load a texture into ResourceManager
	ResourceManager::LoadTexture("textures/example.png", "example");
	
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
	// retrieve a texture from ResourceManager
	ResourceManager::GetTexture("example"); //!ERROR: "example" doesn't exist
	
	// load a texture into ResourceManager
	ResourceManager::LoadTexture("textures/example.png", "example");
	
	// from this point and onwards "example" exists
}
```

(Explain how to use the file path)

(Define Class functions, and header location and the example of function usage)