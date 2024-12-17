The Shader Class is responsible for compiling and editing variables from given source code of shader files such as vertex, fragment, and geometry that are written in GLSL. In order for this class to function it needs OpenGL to be initialized before utilizing this class.

Due note upon compiling the shader, you MUST DELETE it before program exit to avoid memory leaks
```cpp
int main(){
	// extract GLSL code from shader files
	// create shader object
	Shader shader;
	// compile shader code
	shader.compile(vertexCode, fragmentCode, geometryCode);
	
	// use shader and/or edit variables in the shader...
	
	// delete shader
	shader.DeleteShader();
	
	// exit the program
	return 0;
}
```
Refer to GLSL documentation:
* https://www.khronos.org/opengl/wiki/Core_Language_(GLSL)

Header location/class name
```cpp
#include <resourceSystems/resource_shader.hpp>

class Shader{
};
```
## Class Functions:
#### public: Use() -> returns Shader&
* sets the shader as active
* returns reference of the shader itself
* refer to GLSL documentation on its use
```cpp
void func(){
	// extract GLSL code from shader files
	// create shader object
	// compile shader code
	// use the shader
	shader.Use();
}
```
#### public: Compile(const char*, const char*, const char*)
* compiles the shader from given source code which are vertex, fragment, and/or geometry shader
* due note that usually in 2D rendering the Geometry shader is not used so the last parameter is set to "nullptr" by default
* upon compilation completion the compiled shader is set to active
* the function does check for any compilation errors in the given source code
* refer to GLSL documentation on shader compilation
```cpp
void func(){
	// extract GLSL code from shader files
	// create shader object
	// compile shader code
	shader.Compile(vertexCode, fragmentCode,geometryCode);
}
```
#### public: DeleteShader()
* used for deleting the compiled shader, it is highly recommended to delete all shaders before program exit as it may lead to memory leaks
* this function allows for manual shader deletion
```cpp
void func(){
	// extract GLSL code from shader files
	// create shader object
	// compile shader code
	// delete the compiled shader
	shader.DeleteShader();
}
```
