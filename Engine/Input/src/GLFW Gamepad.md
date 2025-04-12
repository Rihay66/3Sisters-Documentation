The Gamepad Manager for GLFW is responsible for connecting and checking for input of game controllers that get connected. 

Refer to GLFW documentation: 
* https://www.glfw.org/docs/latest/ 

### GamepadManager is a Singleton Singleton Class

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

To make a selection of a library to allow for input you must make use of their designated namespace. As well due note that you cannot mix library selection between [[Window]] and [[Input]] as it make cause definition errors thus you must have the same library selection throughout the whole application when using the engine 
```cpp
// example of selecting a library

// choosing to use GLFW
using namespace GLFW;

// call GLFW specific function
getKeyInput(...);

// alternative

// call GLFW specific function using the namespace
GLFW::getKeyInput(...);
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

Similarly, you need to call [[Window]] "runtime()" in order for checking for events detected by the library selected like either GLFW or SDL.

Once the **Gamepad Manager** is initialized you must then create a "Gamepad" component 
```cpp
void func(){
	// initialize gamepad query

	// create gamepad component using GLFW as an example (similar when using SDL)
	GLFW::Gamepad pad;

	// queue the created gamepad to be set
	GLFW::GamepadManager::SetGamepad(pad);
}
```
Due note that there is a difference to how the "Gamepad" [[Component]] is set when using either GLFW or SDL.

For [[GLFW Gamepad]], when setting a gamepad: 
* the manner that it gets set is by within a array from 0 to 15 and when GLFW finds all the game controllers it assigns them starting from 0 and so on depending on how many controllers are connected. Upon disconnecting a controller, rest of the assigned index of other controllers remains the same, however when reconnecting or adding a new controller it replaces the lowest index that isn't occupied by another controller. 

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

Lastly, when connecting a game controller, the "Gamepad" component should have its "isConnected" flag boolean should be **true** meaning its been set successfully. Then you can then use the input functions which are the same for both libraries, GLFW or SDL. To refer to a button or joystick from a controller you can check the commercial controller button macros or the engine's universal macros listed in the shortcut input functions headers

Within the GLFW gamepad there is the **GamepadManager** and the shortcut input functions in separate header locations

Header location/class name:
```cpp
#include <input/managers/sisters_glfw_keyboard_manager.hpp>

namespace GLFW{

class KeyboardManager{
...
};

}
```
## Class Functions:
#### static public: InitializeQuery()
* sets up query event callback, expands a list of connected and disconnected devices
* allows GLFW to detect connected or disconnected game controllers
```cpp
void func(){
	// initialize controller query
	GLFW::GamepadManager::InitializeQuery();
}
```
#### static public: GetGamepadAmount() -> returns int
* returns number of devices that identify as "gamepads" otherwise known as game controllers
* this only returns the number of gamepads that are connected
* REQUIRES: InitializeQuery() needs to be called before using this function
```cpp
void func(){
	// get amount of gamepads connected
	int amount = GLFW::GamepadManager::GetGamepadAmount();
}
```
#### static public: SetGamepad(GLFW::Gamepad&, int)
* 