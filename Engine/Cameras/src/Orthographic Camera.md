The Camera classes are responsible for calculating an orthographic projection onto the screen to allow for 2D rendering. 

Refer to [[Resource Systems]] 
* [[Shader]]
Refer to GLM documentation: 
* http://glm.g-truc.net/0.9.9/api/modules.html

Header location/class name:
```cpp
#include <cameras/ortho_camera.hpp>

class OrthoCamera{
...
};
```
## Class variables:
#### public: glm::vec2 position
* used to refer to the camera's position 
* Refer to GLM documentation: http://glm.g-truc.net/0.9.9/api/modules.html
```cpp
void func(){
	// example: change camera position
	// move camera to the left
	camera.position.x += 1.0f;

	// move camera down
	camera.position.y -= 7.5f;

	// set camera to a specific position
	camera.position = glm::vec2(-5.0f, 1.0f);
}
```

## Class functions:

#### public: setDimensions(unsigned int, unsigned int)
* used to set both the width and height of the camera's view
```cpp
void func(){
	// example: set camera dimensions
	camera.setDimensions(1280, 720);
}
```
#### public: setWidth(unsigned int)
* used to set the width of the camera's view
```cpp
void func(){
	// example: set camera width
	camera.setWidth(1920);
}
```
#### public: setHeight(unsigned int)
* used to set the height of the camera's view
```cpp
void func(){
	// example: set camera height
	camera.setHeight(1080);
}
```
#### public: getWidth() -> returns unsigned int
* used to retrieve the width of the camera's view
```cpp
void func(){
	// example: get the camera width
	unsigned int width = camera.getWidth();
}
```
#### public: getHeight() -> returns unsigned int
* used to retrieve the height of the camera's view
```cpp
void func(){
	// example: get the camera height
	unsigned int height = camera.getHeight();
}
```
#### public virtual: calculateProjectionView([[Shader]]&)
* does calculations of the view and projection to create the ProjectionView and set to given shader
* it allow for updating the [[Shader]]'s projectionView variable
* can be overwritten 
```cpp
void func(){
	// example: calculate the projection view
	
	// load a shader...
	
	// calculate the projection view
	camera.calculateProjectionView(shader);
}
	```