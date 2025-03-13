The input system implementation within the [[3 Sisters-Engine]] is to allow for ease of use and global use, meaning you can check for input at later points in your code without having to keep track of a variable. DUE NOTE: there can be some differences between GLFW and SDL versions of input.

Refer to GLFW documentation: 
* https://www.glfw.org/docs/latest/ 
Refer to SDL documentation: 
* https://wiki.libsdl.org/SDL3/FrontPage

The input system is separated into two handlers either GLFW or SDL. Similarly, the input system has keyboard and gamepad specific functionality. Furthermore they are also split into input functions and "Manager" specific functions where the input functions just check if such button or joystick is being interacted while the "Manager" handles checking for special and IO events.

Within input system, regardless of choosing either GLFW or SDL, there is the **KeyboardManager** and **GamepadManager** which are the "Manager" refered to earlier. Each of these are Static Singletons Classes.

### KeyboardManager and GamepadManager are Singleton Singleton Classes 

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included

For example: including a Static Singleton Class like [[Resource Manager]]

``` cpp
#include <ResourceSystems/Managers/ResourceManager.hpp>
```

From this point the class [[Resource Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <resourcesystems/managers/resource_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	ResourceManager::BindTextures();
}
```

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

When it comes down to initializing **Keyboard Manager** to allow for keyboard input there is a difference between selection of the library:

For [[GLFW keyboard]], you need to pass a reference of the window handle from [[Window-GLFW]]
```cpp
void func(){
	// get window handle from window
	GLFWwindow* handle = window.getWindowHandle();
	// give reference of the window handle to the keyboard manager
	GLFW::KeyboardManager::SetWindowHandle(handle);

	// use keyboard input functions like getKeyInput()...
}
```
For [[SDL keyboard]], you need to pass a reference of the keyboard state holder from [[Window-SDL]]
```cpp
void func(){
	// get keyboard state holder from window
	KeyboardStateHolder* handle = window.getKeyboardState();
	// give reference of the keyboard state to the keyboard manager
	SDL::KeyboardManager::SetKeyboardState(handle);

	// use keyboard input functions like getKeyInput()...
}
```

The keyboard input gets update by [[Window]] when running the "runtime()" which checks for IO events and without it you can't check for any input from the keyboard

Once the **Keyboard Manager** is initialized you can then use the shortcut input functions which are similar across both libraries GLFW and SDL, however due note that to refer to a key on the keyboard differs between the selection of the library

For [[GLFW keyboard]], 
	refer to GLFW keyboard tokens: https://www.glfw.org/docs/3.3/group__keys.html
```cpp
// using window paradigm
void init(){
	// set up keyboard manager
}

void upadte(){
	// check for input from the 'D' key of the keyboard
	if(GLFW::GetKeyInput(GLFW_KEY_D)){
		// do something...
	}
}
```

For [[SDL keyboard]],
	refer to SDL keyboard scancode: https://wiki.libsdl.org/SDL2/SDL_Scancode 
```cpp
// using window paradigm
void init(){
	// set up keyboard manager
}

void update(){
	// check for input from the 'D' key of the keyboard
	if(SDL::GetKeyInput(SDL_SCANCODE_D)){
		// do something...
	}
}
```

When it comes down to initializing **Gamepad Manager** to allow for game controller input there is a difference between selection of the library:

For [[GLFW Gamepad]]
```cpp
void func(){
	// initialize gamepad query
	GLFW::GamepadManager::InitializeQuery();

	// set a gamepad component and use component to check for input 
}
```

For [[SDL Gamepad]], you need to pass reference of the event state from [[Window-SDL]]
```cpp
void func(){
	// get event state from window
	SDLEvent& event = window.getEventState();
	// initialize gamepad query
	SDL::GamepadManager::InitializeQuery(event);
}
```

Similarly, you need to call [[Window]] "runtime()" in order for checking for events detected by the library selected like either GLFW or SDL.

Once the **Gamepad Manager** is initialized you must then create a "Gamepad" component 
```cpp
void func(){
	// initialize gamepad query

	// create gamepad component using SDL as an example (similar when using GLFW)
	SDL::Gamepad pad;

	// queue the created gamepad to be set
	SDL::GamepadManager::SetGamepad(pad);
}
```
Due note that there is a difference to how the "Gamepad" components are set

(explain the difference between GLFW and SDL in how the queue works for them)
For [[GLFW Gamepad]], when using 


Then have it in queue to be set by the **Gamepad Manager** and the call the "PollIO()" to let **Gamepad Manager** assigned all queued components, due note it is recommended to call "PollIO()" continously
```cpp
void func(){
	// initialize gamepad query
	// create gamepad component
	// queue the created gamepad to be set

	// check IO events, to set the queued gaempad component
	GamepadManager::PollIO();
}
```

Lastly, when connecting a game controller, the "Gamepad" component should have its "isConnected" flag boolean should be **true** meaning its been set successfully. Then you can then use (give example on how to get input from gamepad)

Here are all the input modules separated by the library they use:

GLFW:
[[GLFW Gamepad]]
[[GLFW keyboard]]

SDL:
[[SDL Gamepad]]
[[SDL keyboard]]