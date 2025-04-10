The input system implementation within the [[3 Sisters-Engine]] is to allow for ease of use and global use, meaning you can check for input at later points in your code without having to keep track of a variable. DUE NOTE: there can be some differences between GLFW and SDL versions of input.

Refer to GLFW documentation: 
* https://www.glfw.org/docs/latest/ 

Refer to SDL documentation: 
* https://wiki.libsdl.org/SDL3/FrontPage

The input system is separated into two handlers either GLFW or SDL. Similarly, the input system has keyboard and gamepad specific functionality. Furthermore they are also split into input functions and "Manager" specific functions where the input functions just check if such button or joystick is being interacted while the "Manager" handles checking for special and IO events.

Within input system, regardless of choosing either GLFW or SDL, there is the **KeyboardManager** and **GamepadManager** which are the "Manager" refered to earlier. Each of these are Static Singletons Classes.

### KeyboardManager and GamepadManager are Singleton Singleton Classes 

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included

For example: including a Static Singleton Class like [[Texture Manager]]

``` cpp
#include <resourcesystems/managers/texture_manager.hpp>
```

From this point the class [[Texture Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <resourcesystems/managers/texture_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	TextureManager::BindTextures();
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

Once the **Keyboard Manager** is initialized you can then use the shortcut input functions which are similar across both libraries GLFW and SDL, however due note that to refer to a key on the keyboard differs between the selection of the library or you can use the engine's keyboard macros.

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

For using engine macros:
```cpp
// using SDL as an example
// using window paradigm
void init(){
	// set up keyboard manager
}

void update(){
	// check for input from the 'D' key of the keyboard
	if(SDL::GetKeyInput(SISTERS_KEY_D)){
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
Due note that there is a difference to how the "Gamepad" [[Component]] is set when using either GLFW or SDL.

For [[GLFW Gamepad]], when setting a gamepad: 
* the manner that it gets set is by within a array from 0 to 15 and when GLFW finds all the game controllers it assigns them starting from 0 and so on depending on how many controllers are connected. Upon disconnecting a controller, rest of the assigned index of other controllers remains the same, however when reconnecting or adding a new controller it replaces the lowest index that isn't occupied by another controller. 

For [[SDL Gamepad]], when setting a gamepad: 
* the manner that it gets set is by when SDL finds all the game controllers it then assigns the gamepad [[Component]] with the highest priority will be set with the first game controller SDL finds and so forth with the rest of the queued up gamepad [[Component]]s.

When having the [[Component]] in queue to be set by the **Gamepad Manager** and the call the "PollIO()" to let **Gamepad Manager** assigned all queued components, due note it is recommended to call "PollIO()" continuously
```cpp
void func(){
	// initialize gamepad query
	// create gamepad component
	// queue the created gamepad to be set

	// check IO events of game controllers
	GamepadManager::PollIO();
}
```

Lastly, when connecting a game controller, the "Gamepad" component should have its "isConnected" flag boolean should be **true** meaning its been set successfully. Then you can then use the input functions which are the same for both libraries, GLFW or SDL. To refer to a button or joystick from a controller you can check [[GLFW Gamepad]] or [[SDL Gamepad]] as well the engine's universal macros

**DUE NOTE**: the input macros are built around modern and commercial controllers like Playstation, Xbox, and Nintendo and controllers with additional buttons or features aren't fully supported, however do refer to SDL documentation on how to do so as the engine does expose the SDL library.

```cpp
// using SDL as an example, and is similar doing so in GLFW
void func(){
	
	// initialize gamepad query
	// create gamepad component
	// queue the created gamepad to be set
	// check IO events of game controllers

	// check for input, note the button macro
	if(SDL::getButtonInput(gamepad, PLAYSTATION_BUTTON_CROSS)){
		// do something...
	}
}
```

Here are all the input modules separated by library:

GLFW:
[[GLFW Gamepad]]
[[GLFW keyboard]]

SDL:
[[SDL Gamepad]]
[[SDL keyboard]]