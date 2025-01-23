The Entity Component System is a different approach to representing objects or "GameObjects" and using them to keep track and allow for some functionality. Despite its performance benefits, though its design and usage requires a lot more thinking and even getting used to if you've been used to OOP. This general information should give enough information to start thinking about and not only that there will be examples and tutorials showing its usage and application.

### ECS is a Static Singleton Class

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

ECS is comprised of three modules:
* [[Entity]]
* [[Component]]
* [[System]]
Each of these modules work together, all to represent the [[Entity]] which is the ID that represents your object like Player, a UI element, or an Item.

Header location/class name:
```cpp
#include <ecs/ecs.hpp>

class ECS{
...
};
```
## Class functions:
#### public: Init(char)
* used to initialized ECS to allow for usage for [[Component]] and [[System]]
* by default, the char option 'd' is set to allow for checking and printing errors
* the char option 'r' is used to disable for printing and checking for some errors however you must appropriately check that no errors are encountered to fully make use of this option
```cpp
void func(){
	// initialize ECS
	ECS::Init();
}
```

## DUE NOTE!
That documentation for for each of the modules (i.e [[Entity]], [[Component]], [[System]]) will refer to the header that contains all the modules in one:
```cpp
#include <ecs/ecs.hpp>
```
So this page will only explain how to use it, and each of the module pages will explain the details and all functions of ECS as well as further usage

To better explain usage of ECS is better with a step by step on how to use and both explain each modules, it will be done by example: so lets create a Player using ECS able to move around using input. Be sure to review the modules before starting this example

First initialize ECS in order to use it:
```cpp
#include <ecs/ecs.hpp>

void func(){
	// initialize ECS for app usage
	ECS::Init();
}
```

Define components that will be attached to the Player:

Since the Player will be moved around. We'll choose to move the player on a 2D space so the Player needs a component that contains the player's position:
```cpp
// this can be it own header which will be called "playerComponent.hpp"

// representation of the Player's position
struct transform2D{
	float x; // x-axis position
	float y; // y-axis position
};
```

Now we then register the component to allow for usability of it within ECS
```cpp
#include <ecs/ecs.hpp>
// include the define component defined above
#include "playerComponent.hpp"


void func(){
	// initialize ECS for app usage
	ECS::Init();

	// register components
	ECS::RegisterComponent<transform2D>();
}
```

For moving the Player, we need to define a [[System]] which will automatically handle grabbing the player, check the component that is attached to the Player, then update the player transform, and then loop again.

So let's create such [[System]]. 

```cpp
t
```