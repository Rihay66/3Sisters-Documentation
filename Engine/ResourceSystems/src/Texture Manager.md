The Texture Manager is responsible for handling and managing either loading or retrieving [[Texture]]s, SubTextures, and Fonts from a given file location. It provides the loaded resources to be available across the runtime of the application, hence this class is a Static Singleton Class. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

### Texture Manager is a Static Singleton Class

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

Within the texture manager it manages resources by having them be pointing to a given name and once set it then can be used for retrieving the desired resource. 

For Example: Load a texture and give it a name
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

For Example: Texture Manager usage across different files
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

For example: Retrieve and then load a texture of the same name
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
#include <resourcesystems/managers/texture_manager.hpp>

class TextureManager{
...
};
```
## Class Functions:
#### static public: LoadTexture(const char*, std::string, bool) -> [[Texture]]&
* loads (and generates) a texture from file along with a name and optional texture filter option
* returns a reference of the loaded/generated texture for external use
* automatically the given file extension that is given, the function determines if it uses alpha or not
* by default the texture's filter is set to be nearest and it is optional to set it to **true** which sets to be linear
```cpp
void func(){
	// load a texture
	TextureManager::LoadTexture("textures/example.png", "example");
}
```
#### static public: LoadFontTexture(const char*, std::string, bool, std::uint32_t, uint32_t, uint32_t, float, bool) -> CharacterSet&
* loads (and generates) a font texture from file with a specified resolution of the font texture (width and height) and font size along with name and optional texture filter option
* returns a reference of the set of font characters for external use
* by default the texture's filter is set to be nearest and it is optional to set it to **true** which sets to be linear
```cpp
void func(){
	// load a font texture
	TextureManager::LoadFontTexture("fonts/arial.ttf","font", 512, 512, 24.0f);
}
```
#### static public: GenerateSubTexture(std::string, [[Texture]]&, glm::uvec2, glm::uvec2, glm::uvec2) -> returns std::array<glm::vec2, 4>&
* use a loaded texture to create a sub texture along with a name
* you must know the dimensions of the texture
* use the coordinates that start from the bottom left of the texture, the cell size or the portion taken from the coordinate, and by default per pixel is set to 1 which optional can be changed
```cpp
void func(){
	// load a texture
	// create a subtexture
	TextureManager::GenerateSubTexture("bench", benchesTexture, {0.0f, 5.0f}, 20.0f, 20.0f);
}
```
#### static public: GenerateWhiteTexture()
* create a white texture that is named "default"
* this is useful for debugging, placeholder, and/or useful texture without having to create a similar texture by hand.
* when called again does nothing
```cpp
void func(){
	// generate white texture
	TextureManager::GenerateWhiteTexture();
	// get the generated texture
	Texture& tex = TextureManager::GetTexture("default");
}
```
#### static public: GetTextureIndex(std::string) -> int
* retrieves the texture index from a stored texture
* the index of textures are binded through QuadManager's BindTexture()
* useful to [[Quad Renderer]]
```cpp
void func(){
	// load texture
	// get the texture index from stored texture
	int texIndex = TextureManager::GetTextureIndex("default");
	
	// example: use the texture index to render a quad with the loaded texture
	// ...
	QuadRenderer::DrawQuad(texIndex, position, size, rotation, color);
	// ...
}
```
#### static public: GetTexture(std::string) -> returns [[Texture]]&
* used to retrieve a stored texture
```cpp
void func(){
	// load a texture
	// get the loaded texture
	Texture& tex = TextureManager::GetTexture("default");
}
```
#### static public: GetFontTexture(std::string) -> returns CharacterSet&
* used to retrieve a stored font texture map that contains characters and associated font character
```cpp
void func(){
	// load a font
	// get the font texture
	CharacterSet& font = TextureManager::GetFontTexture("font");
}
```
#### static public: GetSubTexture(std::string) -> std::array<glm::vec2, 4>&
* used to retrieve a generated sub texture
```cpp
void func(){
	// load a texture
	Texture& tex = TextureManager::LoadTexture("animals.png","animals");
	// generate a sub texture from the loaded texture
	TextureManager::GenerateSubTexture("animals", tex, {2.0f, 0.0f}, {5.0f,5.0f});
	// get the sub texture
	std::array<glm::vec2, 4>& texCoords = TextureManager::GetSubTexture("cat");
}
```
#### static public: BindTextures()
* used to bind all texture (not fonts) from the texture list to be used by OpenGL so then it can be used by a renderer like [[Quad Renderer]]
* is automatically called by LoadTexture()
* can be called multiple times
```cpp
void func(){
	// load a texture
	// bind all loaded textures, note that LoadTexture already calls this func
	TextureManager::BindTextures();
}
```
