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
* calling this function again causes a warning
```cpp
void func(){
	// initialize ECS
	ECS::Init();
}
```

#### public: CreateEntity() -> returns [[Entity]]
* used to create an entity, returns the value that refers to that create entity
* there's a limit of how many entities can be created in total which is 10000
```cpp
void func(){
	// initialize ECS 

	// create an entity
	Entity entity = ECS::CreateEntity();

	// do something with created entity...
}
```



Once finished reading all that make up the ECS system, you can then move onto learning how to use it all so refer to [[ECS Usage]] to get a better understanding on how to use ECS