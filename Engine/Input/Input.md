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
```

(explain how to initialize keyboard)
(explain how to initialize gamepad)
(give generic examples of using keyboard and gamepad)


[[GLFW Gamepad]]
[[GLFW keyboard]]
[[SDL Gamepad]]
[[SDL keyboard]]