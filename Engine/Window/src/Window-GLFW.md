The Window-GLFW class is responsible for creating a OpenGL context window utilizing the GLFW library which allows for direct/indirect functionality of other components within the engine such as the [[Sprite Renderer]]. 

Refer to GLFW documentation: https://www.glfw.org/docs/latest/ 

Class Functions: (Function and usage example)
#### protected: setTargetTimeStep(double)
- used to set the target frame time between frame, aka max frame time
- the default value for time step is 16.6ms or 60 frames per second
```cpp
// in order to utilize func must use inheritance
void func::Window(){
	// set target time step to 60 frames per second
	setTargetTimeStep(1.0f/60.0f);	
}
```
#### protected: setFixedTimeStep(double)
* used to set the fixed frame time between frame
*  the default value for fixed time step is 16.6ms or 60 frames per second
```cpp
// in order to utilize func must use inheritance
void func::Window(){
	// set target time step to 60 frames per second
	setFixedTimeStep(1.0f/60.0f);	
}
```
#### public virtual: initializeWindow(int, int, const char*)
 * used to initialize the window and it's contexts by default initializes GLFW and creates a Window with OpenGL 4.5 capabilities
 * sets the window handle context which can be retrieved
 * the last parameter, const char*, gives the window a name which is optional
 * Overwriting is not recommended, however due note that runtime(), getDeltaTime(), setUpOpenGL() require GLFW to be initialized and have a created window handle context
```cpp 
void func(){
	// create a class object that inherits from Window
	AppWindow window;
	
	// initialize the window with a size of 800x600
	window.initializeWindow(800, 600);
}

// *Alternative*
void func(){
	// create a class object that inherits from Window
	AppWindow window;
	
	// initialize the window with a size of 800x600 with a name
	window.initializeWindow(800, 600, "My App");
}
```
#### public: getWindowHandle() -> returns glfwWindow*
* used to grab reference to the window handle context
* returns glfwWindow* created from initializeWindow(), else returns nullptr when not called
```cpp
void func(){
	// grab reference to the window handle
	glfwWindow* handle = window.getWindowHandle();
	
	// example: use glfw handle to get keyboard input when pressed
	// refer to GLFW documentation
	if(glfwGetKey(handle, GLFW_KEY_E) == GLFW_PRESS){
		// do something...
	} 
}
```
#### public virtual: getDeltaTime() -> returns float
* returns the current delta time
* Overwriting may need further modifications or accommodation to the update(), stepUpdate(), and render() as it may cause unintended behavior
```cpp 
void func(){
	// obtain the deltatime
	float dt = window.getDeltaTime();
	
	// example: use deltatime to have smooth vector translation 
	player.position.x = movement * dt;
}
```
#### public: getWidth() -> returns unsigned int
* returns the width of the window
* when initializeWindow() is not called, this functions returns 0
```cpp
void func(){
	// obtain the width of the window
	unsigned int w = window.getWidth();
}
```
#### public: getHeight() -> returns unsigned int
* returns the Height of the window
* when initializeWindow() is not called, this functions returns 0
```cpp
void func(){
	// obtain the height of the window
	unsigned int w = window.getHeight();
}
```