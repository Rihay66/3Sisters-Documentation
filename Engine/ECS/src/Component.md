A Component is represented as a data structure that only contains data. So to create a component it would look likes this:

```cpp
// example component with data
struct Component{
	int value;
	float timer;
};
```

These components can be attached to an entity or numerous entities. Each entity can have the same component but they'll have a different instance of that same component, meaning similar components attached to different entities don't affect each other when a change only happens to one component or entity.

Within the [[ECS]] when registering each component also create a "ComponentType" which is just a single bit turned within a 32 bit set. This then allows for signatures that can be then used to filter entities with various components to then find entities with a specific set of signatures, or component types in simpler terms, which is what the [[System]]s.

Within the [[ECS]] there's a limit to how many components there can be which is **32** total components that can be registered and used. 

The following are [[ECS]] functions that concern [[Component]] directly

Header location/class name:
```cpp
#include <ecs/ecs.hpp>

class ECS{
...
};
```
## Class functions:
#### public: AddComponent([[Entity]], 'T')
* used to add a specified [[Component]] to an existing entity
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

#### public: RemoveComponent<'T'>([[Entity]])
* used to remove a [[Component]] from an existing entity
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

#### public: GetComponent<'T'>([[Entity]]) -> returns T&
* used to get reference of an existing entity's [[Component]]
* if the component is not registered then it will return an error
* if the entity doesn't have such component then it will return an error
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

	// get entity component
	Position& pos = ECS::GetComponent<Position>(entity);
	// modify the component
	pos.x += 10.0f;
	pos.y -= 5.0f;
}
```

#### public: CheckComponent<'T'>([[Entity]]) -> returns bool
* used to check if the entity contains given [[Component]] 
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

	// check if entity has component
	if(ECS::CheckComponent<Position>(entity) == true){
		// entity does have component
		// do something...
	}
}
```

#### public: GetComponentType<'T'>() -> returns ComponentType 
* used to get the component type of given [[Component]] 
* ComponentType is a uint8_t where each bit belongs to a registered component
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
	
	// get component type from registered component
	ComponentType type = ECS::GetComponentType<Position>();
}
```
