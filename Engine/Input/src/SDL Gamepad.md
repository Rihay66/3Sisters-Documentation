The gamepad Input for SDL is made available to be utilized when creating a a class that inherits from [[Window]]

Refer to SDL documentation: 
* https://wiki.libsdl.org/SDL3/FrontPage

When creating a class that inherits from [[Window]], calling the default runtime(), and using the window paradigm. You can already utilize the keyboard input functions.
**DISCLAIMER: overriding runtime() means you have to figure out how to check for input on your own and as well renders this page meaningless to read**

To make a selection of a library to allow for input you must make use of their designated namespace. As well due note that you cannot mix library selection between [[Window]] and [[Input]] as it make cause definition errors thus you must have the same library selection throughout the whole application when using the engine 
```cpp
// example of selecting a library

// choosing to use SDL
using namespace SDL;

// call SDL specific function
getButtonInput(...);

// alternative

// call SDL specific function using the namespace
SDL::getButtonInput(...);
```

You need to call [[Window]] "runtime()" in order for checking for events detected such as when connecting or disconnecting a controller by the library selected like either GLFW or SDL.

Once the [[Window]] is initialized you must then create a "Gamepad" component 
```cpp
void func(){
	// create gamepad component using SDL as an example (similar when using SDL)
	SDL::Gamepad pad;

	// queue the created gamepad to be set
	SDL::SetGamepad(&pad);
}
```

Lastly, when connecting a game controller, the "Gamepad" component should have its "isConnected" flag boolean should be **true** meaning its been set successfully. Then you can then use the input functions which are named the same for both libraries, GLFW or SDL. To refer to a button or joystick from a controller you can check the commercial controller button and joystick macros or the engine's universal macros

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

Header location:
```cpp
#include <input/sisters_sdl_gamepad.hpp>

namespace SDL{
...
}
```

## Input Functions:
List of all defined framework input macros defined for each of the buttons and joystick on a gamepad
```
//? Engine specific button macros

#define SISTER_BUTTON_SOUTH 0
#define SISTER_BUTTON_EAST 1
#define SISTER_BUTTON_WEST 2
#define SISTER_BUTTON_NORTH 3
#define SISTER_BUTTON_LEFT_BUMPER 4
#define SISTER_BUTTON_RIGHT_BUMPER 5
#define SISTER_BUTTON_BACK 6
#define SISTER_BUTTON_START 7
#define SISTER_BUTTON_GUIDE 8
#define SISTER_BUTTON_LEFT_THUMB 9
#define SISTER_BUTTON_RIGHT_THUMB 10
#define SISTER_BUTTON_DPAD_UP 11
#define SISTER_BUTTON_DPAD_RIGHT 12
#define SISTER_BUTTON_DPAD_DOWN 13
#define SISTER_BUTTON_DPAD_LEFT 14

#define SISTER_JOYSTICK_LEFT_X 0
#define SISTER_JOYSTICK_LEFT_Y 1
#define SISTER_JOYSTICK_RIGHT_X 2
#define SISTER_JOYSTICK_RIGHT_Y 3
#define SISTER_LEFT_TRIGGER 4
#define SISTER_RIGHT_TRIGGER 5

//? Playstation specific button macros

#define PLAYSTATION_BUTTON_CROSS 0
#define PLAYSTATION_BUTTON_CIRCLE 1
#define PLAYSTATION_BUTTON_SQUARE 2
#define PLAYSTATION_BUTTON_TRIANGLE 3
#define PLAYSTATION_BUTTON_L1 4
#define PLAYSTATION_BUTTON_R1 5
#define PLAYSTATION_BUTTON_SELECT 6
#define PLAYSTATION_BUTTON_START 7
#define PLAYSTATION_BUTTON_PS 8
#define PLAYSTATION_BUTTON_L3 9
#define PLAYSTATION_BUTTON_R3 10
#define PLAYSTATION_BUTTON_DPAD_UP 11
#define PLAYSTATION_BUTTON_DPAD_RIGHT 12
#define PLAYSTATION_BUTTON_DPAD_DOWN 13
#define PLAYSTATION_BUTTON_DPAD_LEFT 14

#define PLAYSTATION_JOYSTICK_LEFT_X 0
#define PLAYSTATION_JOYSTICK_LEFT_Y 1
#define PLAYSTATION_JOYSTICK_RIGHT_X 2
#define PLAYSTATION_JOYSTICK_RIGHT_Y 3
#define PLAYSTATION_L2_TRIGGER_LEFT 4
#define PLAYSTATION_R2_TRIGGER_RIGHT 5

//? Xbox specific button macros

#define XBOX_BUTTON_A 0
#define XBOX_BUTTON_B 1
#define XBOX_BUTTON_X 2
#define XBOX_BUTTON_Y 3
#define XBOX_BUTTON_LEFT_BUMPER 4
#define XBOX_BUTTON_RIGHT_BUMPER 5
#define XBOX_BUTTON_BACK 6
#define XBOX_BUTTON_START 7
#define XBOX_BUTTON_GUIDE 8
#define XBOX_BUTTON_LEFT_THUMB 9
#define XBOX_BUTTON_RIGHT_THUMB 10
#define XBOX_BUTTON_DPAD_UP 11
#define XBOX_BUTTON_DPAD_RIGHT 12
#define XBOX_BUTTON_DPAD_DOWN 13
#define XBOX_BUTTON_DPAD_LEFT 14

#define XBOX_JOYSTICK_LEFT_X 0
#define XBOX_JOYSTICK_LEFT_Y 1
#define XBOX_JOYSTICK_RIGHT_X 2
#define XBOX_JOYSTICK_RIGHT_Y 3
#define XBOX_LEFT_TRIGGER 4
#define XBOX_RIGHT_TRIGGER 5

//? Nintendo specific button macros

#define NINTENDO_BUTTON_B 0
#define NINTENDO_BUTTON_A 1
#define NINTENDO_BUTTON_Y 2
#define NINTENDO_BUTTON_X 3
#define NINTENDO_BUTTON_LEFT_BUMPER 4
#define NINTENDO_BUTTON_RIGHT_BUMPER 5
#define NINTENDO_BUTTON_MINUS 6
#define NINTENDO_BUTTON_PLUS 7
#define NINTENDO_BUTTON_GUIDE 8
#define NINTENDO_BUTTON_LEFT_THUMB 9
#define NINTENDO_BUTTON_RIGHT_THUMB 10
#define NINTENDO_BUTTON_DPAD_UP 11
#define NINTENDO_BUTTON_DPAD_RIGHT 12
#define NINTENDO_BUTTON_DPAD_DOWN 13
#define NINTENDO_BUTTON_DPAD_LEFT 14

#define NINTENDO_JOYSTICK_LEFT_X 0
#define NINTENDO_JOYSTICK_LEFT_Y 1
#define NINTENDO_JOYSTICK_RIGHT_X 2
#define NINTENDO_JOYSTICK_RIGHT_Y 3
#define NINTENDO_LEFT_TRIGGER 4
#define NINTENDO_RIGHT_TRIGGER 5

//? Generic specific button macros
#define GENERIC_GLOBAL_BUTTON_LAST 14
```

#### getGamepadAmount() -> returns int
* returns number of devices that identify as "gamepads" otherwise known as game controllers
* this only returns the number of gamepads that are connected
```cpp
void func(){
	// get amount of gamepads connected
	int amount = SDL::getGamepadAmount();
}
```

#### setGamepad(SDL::Gamepad&, int)
* add a gamepad reference that can be filled from the list of queried gamepads
* by default the index is 0 which picks the first gamepad set to be set, any other number is causes the gamepad to be set later until a gamepad with index of 0 is set
* Gamepad component is set the moment a valid controller is connected either at start of application or during runtime
```cpp
// assuming this functional gets called through the window paradigm
void func(){
	// create gamepad component using SDL as an example (similar when using SDL)
	SDL::Gamepad pad;

	// queue the created gamepad to be set
	SDL::setGamepad(&pad);
}
```

### NOTE: enableGamepad(int) and disableGamepad(int) are used by [[window]]

### getButtonInput(SDL::Gamepad&, int) -> returns bool
* button callback of the gamepad which checks for the given button
* requires a set gamepad
```cpp
void func(){
	//** Assuming the gamepad is set
	
	// example: checking for button input
	if(getButtonInput(&pad, PLAYSTATION_BUTTON_SQUARE)){
		// do something...
	}
}
```

### getAxisRawInput(SDL::Gamepad&, int, float) -> returns float
* axis callback of the gamepad which returns a incremental value when there is input of the given key
* requires a set gamepad
* the value returned is a **floating** number within in the range from -1.0f to 1.0f
* by default the deadzone value is 0.5f and the valid range for deadzone is 0.0f to 1.0f
```cpp
void func(){
	//** Assuming the gamepad is set
	
	// example: checking for axis input
	if(getAxisRawInput(&pad, XBOX_RIGHT_TRIGGER) > 0.0f){
		// do something...
	}
	
	/*Alternative*/
	
	// get the value of the direction obtain from the axis input
	float direction = getAxisRawInput(&pad, SISTER_JOYSTICK_LEFT_X);
	// example: use the direction with the movement
	movement = direction * speed;
}
```

#### getAxisInput(SDL::Gamepad&, int, float) -> returns float
* axis callback of the gamepad which returns a incremental value when there is input of the given key
* requires a set gamepad
* the value returned is a **whole** number within in the range from -1.0f to 1.0f
* by default the deadzone value is 0.5f and the valid range for deadzone is 0.0f to 1.0f
```cpp
void func(){
	//** Assuming the gamepad is set
	
	// example: checking for axis input
	if(SDL::getAxisInput(&pad, XBOX_RIGHT_TRIGGER) > 0.0f){
		// do something...
	}
	
	/*Alternative*/
	
	// get the value of the direction obtain from the axis input
	float direction = SDL::getAxisInput(&pad, SISTER_JOYSTICK_LEFT_X);
	// example: use the direction with the movement
	movement = direction * speed;
}
```