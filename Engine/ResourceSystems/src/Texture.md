The Texture class is responsible for generating and keeping track of a OpenGL texture given image data from a file. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

Note that by default:
* Internal and Image format are set -> GL_RGB
* Wrapping mode on both S and T axis -> GL_REPEAT
* Filter Max and Min -> GL_NEAREST 

Due note that upon generating a texture, you MUST DELETE it before program exit to avoid memory leaks
```cpp
int main(){
	// get image data from file
	// create texture object
	Texture tex;
	// generate texture from image data
	tex.Generate(width, height, data);
	
	// Use the texture...
	
	// delete the generated texture
	tex.DeleteTexture();
	
	// exit the program
	return 0;
}
```

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

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
* this function is to allow support for legacy OpenGL as the modules within the Engine don't necessarily utilize the texture ID but the texture ID index generated through the [[Texture Manager]] and seek info about how the texture ID index is used in [[Usage]]
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
#### public: SetTextureInternalFormat(unsigned int)
* used to set the internal format of the texture
* refer to OpenGL documentation for which formats can be applied
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// set texture internal format
	tex.SetTextureInternalFormat(GL_RGB);
}
```
#### public: SetTextureImageFormat(unsigned int)
* used to set the image format of the texture
* refer to OpenGL documentation for which formats can be applied
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// set texture image format
	tex.SetTextureImageFormat(GL_RGB);
}
```
#### public: SetTextureFilterMin(unsigned int)
* used to set the minimum filter mode of the texture
* refer to OpenGL documentation for which filter can be applied
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// set texture minimum filter mode
	tex.SetTextureFilterMin(GL_LINEAR);
}
```
#### public: SetTextureFilterMax(unsigned int)
* used to set the maximum filter mode of the texture
* refer to OpenGL documentation for which filter can be applied
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// set texture max filter mode
	tex.SetTextureFilterMin(GL_NEAREST);
}
```
#### public: SetWrapS(unsigned int)
* used to set the wrap mode on the S axis of the texture
* refer to OpenGL documentation for which mode can be applied
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// set texture wrap mode
	tex.SetWrapS(GL_REPEAT);
}
```
#### public: SetWrapT(unsigned int)
* used to set the wrap mode on the T axis of the texture
* refer to OpenGL documentation for which mode can be applied
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// set texture wrap mode
	tex.SetWrapT(GL_REPEAT);
}
```
#### public: GetID() -> returns unsigned int&
* used to retrieve a reference to the ID of the texture
* refer to OpenGL documentation for how to use the ID
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// generate texture
	// get reference to the texture ID
	unsigned int& ID = tex.GetID();
}
```
#### public: GetWidth() -> returns unsigned int
* used to retrieve the pixel width of the texture
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// generate texture
	// get width of the texture
	unsigned int width = tex.GetWidth();
}
```
#### public: GetHeight() -> returns unsigned int
* used to retrieve the pixel height of the texture
```cpp
void func(){
	// retrieve image data from file
	// create Texture object
	// generate texture
	// get height of the texture
	unsigned int width = tex.GetHeight();
}
```