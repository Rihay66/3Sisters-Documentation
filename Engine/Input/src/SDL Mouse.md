The Mouse input for SDL is made available to be utilized when creating a class that inherits from [[Window]]

Refer to SDL documentation:
* https://wiki.libsdl.org/SDL3/FrontPage

When creating a class that inherits from [[Window]], calling the default "runtime()", and using the window paradigm. You can already utilize the mouse functions.
**DISCLAIMER: overriding "runtime()" means you have to figure out how to check for input on your own and as well renders this page meaningless to read**

To make a selection of a library to allow for input you must make use of their designated namespace. As well due note that you cannot mix library selection between [[Window]] and [[Input]] as it make cause definition errors thus you must have the same library selection throughout the whole application when using the engine 
```cpp
// example of selecting a library

// choosing to use SDL
using namespace SDL;

// call SDL specific function
getMousePosition(...);

// alternative

// call SDL specific function using the namespace
SDL::getMousePosition(...);
```

It is also imperative to mentions about the input from the scroll wheel so when there's scroll wheel input moves it is set to the maximum value like 1.5f and the direction is also given by forward scroll being **positive** and backward scroll being **negative** and [[Window]] "runtime()" in the default definition handles slowing down the scroll wheel received input to zero at a rate which can be changed by calling some functions in [[Window]]. 

When using the mouse button input function you are allowed to to use macros from both official GLFW or Framework provide macros.

For using GLFW macros:
* For mouse buttons refer to GLFW Documentation https://www.glfw.org/docs/latest/group__buttons.html
```cpp
void AppWindow::update(){
	// check for input from the left mouse button
	if(GLFW::getMouseInput(GLFW_MOUSE_BUTTON_LEFT)){
		// do something...
	}
}
```
For using Framework macros:
```cpp
void AppWindow::update(){
	// check for input from the left mouse button
	if(GLFW::getMouseInput(SISTER_MOUSE_BUTTON_LEFT)){
		// do something...
	}
}
```

Header location: 
```cpp
#include <input/sisters_glfw_mouse.hpp

namespace GLFW{
...
}
```
## Input Functions:
List of all defined framework mouse button input macros defined for each of the buttons on a mouse
```
//? Engine renamed mouse scancode to allow for mouse inputs

#define SISTER_MOUSE_BUTTON_LEFT 0
#define SISTER_MOUSE_BUTTON_RIGHT 1
#define SISTER_MOUSE_BUTTON_MIDDLE 2
#define SISTER_MOUSE_BUTTON_X1 3
#define SISTER_MOUSE_BUTTON_X2 4
#define SISTER_MOUSE_BUTTON_X3 5
#define SISTER_MOUSE_BUTTON_X4 6
#define SISTER_MOUSE_BUTTON_X5 7
```

#### getMousePosition(double*, double*)
* used to check the current mouse position within the window area
* if the mouse cursor is outside the window area, the default returned value will be what it was last
* the position starts from the **Top Left** corner of the window area
```cpp
void func(){
	// variables containing positions of the mouse
	double x, y;
	// get the mouse position
	GLFW::getMousePosition(&x, &y);
	
	// variable "x" and "y" now contain the position of the mouse
}
```

#### getMouseInput(unsigned int) -> returns bool
* used to check for the mouse button input
* when the mouse cursor is outside the window area, the default returned value is false
```cpp
void func(){
	// check for mouse right button press
	if(GLFW::getMouseInput(GLFW_MOUSE_BUTTON_RIGHT)){
		// do something..
	}
	
	// alternative
	
	// check for mouse right button press
	if(GLFW::getMouseInput(SISTER_MOUSE_BUTTON_RIGHT)){
		// do something..
	}
}
```

#### getMouseWheelYRaw() -> returns double
* used to get the raw value of the vertical mouse wheel input
* the value returned has a direction being forward scroll being **positive** and backward scroll being **negative**
* due note that after receiving input, slowly by frame it is set to zero by [[Window]] by "runtime()" default definition
```cpp
void func(){
	// get the raw mouse wheel input
	double wheel_y = GLFW::getMouseWheelYRaw();
}
```

#### getMouseWheelY() -> return bool
* used to get any input from the vertical mouse wheel input
* the value returned gives **no direction**
* due note that after receiving input, slowly by frame it is set to zero by [[Window]] by "runtime()" default definition
```cpp
void func(){
	// get the mouse wheel input
	bool wheel_y = GLFW::getMouseWheelY();
}
```

#### getMouseFocus() -> return bool
* used to get the status of the mouse cursor if it's within the window area
```cpp
void func(){
	// check if the mouse is within the window area
	if(GLFW::getMouseFocus()){
		// do something... 
	}
}
```