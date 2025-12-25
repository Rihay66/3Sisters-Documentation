The Window-GLFW class is responsible for creating a OpenGL context window utilizing the GLFW library which allows for direct/indirect functionality of other components within the engine such as the [[Quad Renderer]]. 

Refer to GLFW documentation: 
* https://www.glfw.org/docs/latest/ 

Refer to OpenGL documentation: 
* https://www.opengl.org/Documentation/Documentation.html 
* https://www.khronos.org/opengl/wiki/Main_Page

Header location/namespace/class name
```cpp
#include <window/sisters_glfw_window.hpp>

namespace GLFW{
class Window{
...
};
}
```
## Class Functions:
#### protected: setTargetTimeStep(double)
- used to set the target frame time between frame, aka max frame time
- the default value for time step is 16.6ms or 60 frames per second
```cpp
// in order to utilize function must use inheritance
void AppWindow::func(){
	// set target time step to 60 frames per second
	setTargetTimeStep(1.0f/60.0f);	
}
```
#### protected: setFixedTimeStep(double)
* used to set the fixed frame time between frame
* the default value for fixed time step is 16.6ms or 60 frames per second
```cpp
// in order to utilize function must use inheritance
void AppWindow::func(){
	// set target time step to 60 frames per second
	setFixedTimeStep(1.0f/60.0f);	
}
```
#### protected virtual: additionalWindowOptions()
* used for adding additional GLFW window hints
* this function gets called by initializeWindow()
```cpp
// in order to utilize function must use inheritance
void AppWindow::additionalWindowOptions(){
	// add additional window options
	// refer to GLFW documentation
	
	// example: upon window creation, maximize the window
	glfwWindowHint(GLFW_MAXIMIZED, GLFW_TRUE);
}
```
#### protected virtual: setUpOpenGL()
* used for setting up OpenGL rendering
* by default it is defined to allow for 2D rendering
```cpp
// in order to utilize function must use inheritance
void AppWindow::setUpOpenGL(){
	// set opengl rendering
	// refer to OpenGL documentation
	
	// example: set up rendering for 2D
    glEnable(GL_BLEND);
    glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);
}
```
#### protected:  getFrameDuration() -> returns double
* used to retrieve how long a frame took
* variable is **only** populated by runtime(), unless overwrite of runtime() makes use of the variable
```cpp
void func(){
	// create a class object that inherits from Window
	AppWindow window;
	
	// initialize the window with a size of 800x600
	window.initializeWindow(800, 600);
	
	// call runtime
	window.runtime();
}

// in order to utilize function must use inheritance
void AppWindow::func(){
	// retrieve length of frame
	double frame_length = getFrameDuration();
}
```
#### protected:  getCurrentFrame() -> returns double
* used to retrieve the current frame
* variable is **only** populated by runtime(), unless overwrite of runtime() makes use of the variable
```cpp
void func(){
	// create a class object that inherits from Window
	AppWindow window;
	
	// initialize the window with a size of 800x600
	window.initializeWindow(800, 600);
	
	// call runtime
	window.runtime();
}

// in order to utilize function must use inheritance
void AppWindow::func(){
	// retrieve length of frame
	double frame_length = getCurrentFrame();
}
```
#### public virtual: initializeWindow(int, int, const char*)
 * used to initialize the window and it's contexts by default initializes GLFW and creates a Window with OpenGL 4.5 capabilities
 * sets the window handle context which can be retrieved
 * the last parameter, const char*, gives the window a name which is optional
 * during initialization it calls additionalWindowOptions() and setUpOpenGL()
 * overwriting is not recommended, however due note that runtime(), getDeltaTime(), setUpOpenGL() require GLFW to be initialized and have a created window handle context
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
* returns glfwWindow* created from initializeWindow(), else returns nullptr when initializeWindow() is never called
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
* overwriting may need further modifications or accommodation to the update(), stepUpdate(), and render() as it may cause unintended behavior
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
#### public virtual: init()
* used to call functions that handle the loading of shaders, textures, and objects
* must at least be called once
```cpp
// in order to utilize function must use inheritance
void AppWindow::init(){
	// example: initialize a player object
	player.init();
}
```
#### public virtual: stepUpdate(double)
* used to update Physics, ticks systems, or other at a fixed time step
* the parameter of double is the calculate fixed time step value
```cpp
// in order to utilize function must use inheritance
void AppWindow::stepUpdate(double ts){
	// example: make a position vector move left over time
	position.x += 5.0f * ts
}
```
#### public virtual: update()
* used to update logic, custom events, and other at the target time step
```cpp
// in order to utilize function must use inheritance
void AppWindow::update(){
	// example: check a list of player of which one is not alive
	for(auto& plr: players){
		if(plr.isAlive == false){
			//do something...
		}
	}
}
```
#### public virtual: render(double)
* used to render things on the screen
* the parameter is used for linear interpolation to render things smoothly no matter the target time step
```cpp
#include <engine/sprite_renderer.hpp>

// in order to utilize function must use inheritance
void AppWindow::render(double alpha){
	// example: flush all acumulated quads
	SpriteRenderer::FlushQuads();
}
```
#### public virtual: runtime()
* calls Init() once, then loops through getDeltaTime(), update(), stepUpdate(), and render()
* in addition it calls calls GLFW poll events, swap buffers and clears the OpenGL color buffer
* if overwritten, may need to apply calculations of time step, fixed time step and accumulator yourself
```cpp
void func(){
	// call runtime
	window.runtime();
}
```