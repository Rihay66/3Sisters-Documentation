The Camera classes are responsible for calculating projection onto the screen. 
These classes are objects and they keep track of their own position in world space.

Before using this class YOU MUST have a loaded shader and in that shader it must have the following variable called:
``` 
uniform mat4 projectionView;
```
fortunately, the default shaders that the engine comes with does contain this "projectionView" 

Refer to [[Resource Systems]] on how to get a shader loaded

The functions and variables that are common between them 
* **position
	* this variable editable variable refers to the camera's position
	* depending on the type of camera it's either a glm::vec2 or glm::vec3
	refer to GLM documentation: http://glm.g-truc.net/0.9.9/api/modules.html
* **calculateProjectionView([[shader]])
	* does calculations of the view and projection to create the ProjectionView and set to given shader
	* in the calculation it includes the position of the camera

## How to use the camera

stationary camera
```cpp
void func(){
	// load a shader...
	
	// create a camera object
	// example: using Orthographic camera
	OrthoCamera camera;
	
	// set dimensions on the camera
	camera.setDimensions(1280, 720);
	
	// calculate the projection view once
	camera.calculateProjectionView(shader);
}
```

moving camera
```cpp
void func(){
	// load a shader...
	
	// create a camera object
	// example: using Orthographic camera
	OrthoCamera camera;
	
	// set dimensions on the camera
	camera.setDimensions(1280, 720);
	
	// calculate the projection view 
	camera.calculateProjectionView(shader);
	
	// move the camera up
	camera.position.y += 2.5f;
	
	// recalculate the projection view again for the change to take effect
	camera.calculateProjectionView(shader);
}
```


[[Orthographic Camera]]