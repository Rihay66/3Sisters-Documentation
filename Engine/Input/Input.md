The input system implementation within the [[3 Sisters-Framework-Engine]] is to allow for ease of use and global use, meaning you can check for input at later points in your code. DUE NOTE: there can be some differences between GLFW and SDL versions of input.

Refer to GLFW documentation: 
* https://www.glfw.org/docs/latest/ 

Refer to SDL documentation: 
* https://wiki.libsdl.org/SDL3/FrontPage

	When creating a class that inherits from [[Window]], calling the default "runtime()", and using the window paradigm. You can already utilize the keyboard, mouse, and controller input functions.
**DISCLAIMER: overriding "runtime()" means you have to figure out how to check for input on your own and as well renders this page meaningless to read**

To make a selection of a library to allow for input, either GLFW or SDL, you must make use of their designated namespace. As well due note that you cannot mix library selection between [[Window]] and [[Input]] as it make cause definition errors thus you must have the same library selection throughout the whole application when using the engine 
```cpp
// example of selecting a library

// choosing to use GLFW
using namespace GLFW;

// call GLFW specific function
getKeyInput(...);

// alternative

// call GLFW specific function using the namespace
GLFW::getKeyInput(...);

// choosing to use SDL
using namespace SDL;

// call SDL specific function
getKeyInput(...);

// alternative

// call SDL specific function using the namespace
SDL::getKeyInput(...);
```

### Keyboard
The keyboard input gets updated by [[Window]] when running the "runtime()" which checks for IO events and without it you can't check for any input from the keyboard

For [[GLFW keyboard]], 
	refer to GLFW keyboard tokens: https://www.glfw.org/docs/3.3/group__keys.html
```cpp
void update(){
	// check for input from the 'D' key of the keyboard
	if(GLFW::GetKeyInput(GLFW_KEY_D)){
		// do something...
	}
}
```

For [[SDL keyboard]],
	refer to SDL keyboard scancode: https://wiki.libsdl.org/SDL2/SDL_Scancode 
```cpp
void update(){
	// check for input from the 'D' key of the keyboard
	if(SDL::GetKeyInput(SDL_SCANCODE_D)){
		// do something...
	}
}
```

For using engine macros:
```cpp
void update(){
	// check for input from the 'D' key of the keyboard
	if(SDL::GetKeyInput(SISTERS_KEY_D)){
		// do something...
	}
}
```
### Mouse
For Mouse inputs, work similarly to Keyboard inputs though **DUE NOTE**: there are few differences between SDL and GLFW versions though their functionality are exactly the same

### Gamepad
The gamepad get updated by [[Window]] when running the "runtime()" which checks for connected, disconnected, and input for any valid controller.

Similarly, once the [[Window]] is initialized you must then create a "Gamepad" component to then be able to check for input from that component
```cpp
void func(){
	// create gamepad component using SDL as an example (similar when using SDL)
	SDL::Gamepad pad;

	// queue the created gamepad to be set
	SDL::SetGamepad(&pad);
}
```

Lastly, when connecting a game controller, the "Gamepad" component should have its "isConnected" flag boolean should be **true** meaning its been set successfully. Then you can then use any input functions which are named the same for both libraries, GLFW or SDL. To refer to a button or joystick from a controller you can check the commercial controller button and joystick macros or the engine's universal macros

For using GLFW macros:
* For buttons refer to GLFW Documentation https://www.glfw.org/docs/latest/group__gamepad__buttons.html
* For axis refer to GLFW Documentation https://www.glfw.org/docs/latest/group__gamepad__axes.html
For using SDL macros:
* For buttons refer to SDL Documentation: https://wiki.libsdl.org/SDL3/SDL_GamepadButton 
* For axis refer to SDL Documentation: https://wiki.libsdl.org/SDL3/SDL_GamepadAxis
```cpp
void func(){
	// created a gamepad component
	// queue the created gamepad to be set
	
	//** Assuming a gamepad is connected and is set
	
	// example: checking for button input
	if(SDL::getButtonInput(&pad, SDL_GAMEPAD_BUTTON_SOUTH)){
		// do something...
	}
	// example: checking for axis input
	if(SDL::getAxisInput(&pad, SDL_GAMEPAD_AXIS_LEFTX) > 0.0f){
		// do something...
	}
}
```

For using Framework macros:
```cpp
void func(){
	// created a gamepad component
	// queue the created gamepad to be set
	
	//** Assuming a gamepad is connected and is set
	
	// example: checking for button input
	if(SDL::getButtonInput(&pad, SISTER_BUTTON_SOUTH)){
		// do something...
	}
	// example: checking for axis input
	if(SDL::getAxisInput(&pad, SISTER_JOYSTICK_LEFT_X) > 0.0f){
		// do something...
	}
}
```
There is also available, if it makes it easier to read, platform specific macros also provided from the Framework macros as well
```cpp
void func(){
	// created a gamepad component
	// queue the created gamepad to be set
	
	//** Assuming a gamepad is connected and is set
	
	// example: checking for button input
	if(SDL::getButtonInput(&pad, PLAYSTATION_BUTTON_SQUARE)){
		// do something...
	}
	// example: checking for axis input
	if(SDL::getAxisInput(&pad, XBOX_RIGHT_TRIGGER) > 0.0f){
		// do something...
	}
}
```

**DUE NOTE**: the input macros are built around modern and commercial controllers like Playstation, Xbox, and Nintendo however controllers with additional buttons or features aren't fully supported, however do refer to SDL or GLFW documentation on how to do so as the engine does expose the SDL and GLFW libraries.

Here are all the input modules separated by library:

GLFW:
[[GLFW Gamepad]]
[[GLFW keyboard]]
[[GLFW Mouse]]

SDL:
[[SDL Gamepad]]
[[SDL keyboard]]
[[SDL Mouse]]