The Texture class is responsible for generating and keeping track of a OpenGL texture given image data from a file. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

Note that by default:
* Internal and Image format are set -> GL_RGB
* Wrapping mode on both S and T axis -> GL_REPEAT
* Filter Max and Min -> GL_NEAREST 

Due note that once generating a texture, you MUST DELETE it before program exit
```cpp
int main(){
	// get image data from file
	// load a texture
	Texture tex;
	tex.Generate(width, height, data);
	
	// Use the texture...
	
	// delete the generated texture
	tex.DeleteTexture();
	
	// exit the program
	return 0;
}
```

Refer to OpenGL documentation: 
	https://www.opengl.org/Documentation/Documentation.html 

Header location/class name
```cpp
#include <resourceSystems/resource_texture.hpp>

class Texture{
};
```
## Class Functions:
#### public: Generate(unsigned int, unsigned int, const char*)
* used for generating a texture given from the size and data of a image file
* due note that any change to the texture format, filter, or wrap must be done before, if not you'll have to delete the texture and regenerate the texture to apply the changes
```cpp
void func(){
	// retrieve image data from file

	// create Texture object
	Texture tex;

	// generate the texture using the image data
	tex.Generate(imageWidth, imageHeight, imageData);

	// use the texture...
}
```
#### public: BindTexture()
* used for binding the texture before rendering
* this function is to allow support for legacy OpenGL as the modules within the Engine don't necessarily utilize the texture ID but the texture ID index generated through the [[Resource Manager]] and seek info about how the texture ID index is used in [[Usage]]
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// generate texture using image data

	// bind the texture
	tex.BindTexture();

	// render with the binded texture...
}
```
#### public: DeleteTexture()
* used for deleting the generated texture, it is highly recommended to delete all textures before program exit as it may lead to memory leaks
* this function is to allow for manual texture deletion
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// generate texture using image data

	// delete texture before program deletion
	tex.DeleteTexture();
}
```