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
Helpful definitions:
'T' - refers to an arbitrary type
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

#### public: DestroyEntity([[Entity]])
* used to destroy a given entity
* it also removes all components attached to that entity
* avoid storing important data or pointers in components that are then onto the entity as it will be lost and may cause a segfault when trying to access the pointer that was on the entity
```cpp
void func(){
	// initialize ECS
	// create an entity
	// optional: add component to entity=

	// destroy entity
	ECS::DestroyEntity(entity);
}
```

#### public: RegisterComponent<'T'>()
* used to register a specified component for [[Entity]] and [[System]] usage
* due note that without registering a component, you can't add such component to an entity or create signatures for systems
```cpp
// define a component
// EX: test component called 'Position' contains a float variables
struct Position{
	float x, y;
};

void func(){
	// initialize ECS
	
	// register component
	ECS::RegisterComponent<Position>();
	
	// add component to an entity or create signature for system...
}
```

#### public: AddComponent([[Entity]], 'T')
* used to add a specified component to an existing entity
* due note that the specified component must be registered beforehand in order for it work
* When adding components to an entity, you can add multiple components at the same time and you can initialize the values of the components all within the same line of code
```cpp
// define components
// EX: the following components are used as example
struct Position{
	float x, y;
};

struct Texture{
	int ID;
};

void func(){
	// initialize ECS
	// register component 'Position'
	// create entity

	// add component to entity
	ECS::AddComponent(entity, Position{});
}

// *Alternative*
void func(){
	// initialize ECS
	// register component 'Position'
	// create entity

	// add component to entity, and initalize values
	ECS::AddComponent(entity, Position{
		.x = 10,
		.y = 20
	});
}

// *Alternative*
void func(){
	// initialize ECS
	// register components
	// create entity

	// add multiple component to entity, and initalize values
	ECS::AddComponent(entity, 
	Position{
		.x = 10,
		.y = 20
	}, 
	Texture{
		.ID = 3
	});
}
```

#### public: RemoveComponent<'T'>(Entity)
* used to remove a component from an existing entity
* if the component is not registered then it will return an error
```cpp
// define components
// EX: the following components are used as example
struct Position{
	float x, y;
};

void func(){
	// initialize ECS
	// register component
	// create entity
	// add component to entity

	// remove component from entity
	ECS::RemoveComponent<Position>(entity);
}

```

Once finished reading all that make up the ECS system, you can then move onto learning how to use it all so refer to [[ECS Usage]] to get a better understanding on how to use ECS