An Entity is represented as an ID, a number like 2 or 19, that is "linked" to an array of components like a position or texture component. This can be used to logically represent a Player, a UI element, or an Item.

Entity is of type unsigned integer of 32-bit, the name "Entity" is an alias that points to that type. Thus declaring an Entity is the same as declaring said mentioned type.

```cpp
// with alias
Entity e;
// without the alias
std::uint32_t u;

// both 'e' and 'u' are the same type
```

In ECS system handles giving an entity an ID, attaching components, and allow those entities with certain components (called a signature) that are used by systems.

There's a limit to how many entities there can be which can be found in
```cpp
// location of the header
#include <ecs/types/entity.hpp>

...
// define the maximum amount of entites
const Entity MAX_ENTITIES = 10000;
```
and this is because we need to allocate how much static memory we need for each of entities to avoid having to do so during runtime.

The following are [[ECS]] functions that concern [[Entity]] directly

Header location/class name:
```cpp
#include <ecs/ecs.hpp>

class ECS{
...
};
```
## Class functions:

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
