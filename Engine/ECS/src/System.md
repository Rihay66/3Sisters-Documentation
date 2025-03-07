 System is sort of a manager that is able to access all entities that have a set of specific components that is required by the system in order to manipulate the entities.

To define a system it needs to inherit from System and then you can define functions, optionally variables as well, that do something with the added entities that match a signature, or specific set of [[Component]]s
```cpp
// you can include the ECS header directly 
#include <ecs/ecs.hpp>

class ExampleSystem : public System{
	public:
		// some function that does something with entities
		void func();
};
```

Once defining a system we also need a way to refer and call functions from it safely without having to deal deleting the pointer thus we need to define a *alias* that just refers to an instance of the system we just created
```cpp
// you can include the ECS header directly 
#include <ecs/ecs.hpp>

class ExampleSystem : public System{
	...
};

// define alias
using ExampleSystem_Ptr = std::shared_ptr<ExampleSystem>;
```

To use the alias in C++ is similar to defining a variable
```cpp
// create reference to of the system
ExampleSystem_Ptr ex;

// to set the system
ex = some_func();

// to call a function of the system
ex->update_entities();
```

Then we need to register the System into [[ECS]] so it can automatically give entities to the system we created that have a specific set of [[Component]]s  using "RegisterSystem()"
```cpp
void func(){
	// initialize ECS

	// register system
	ECS::RegisterSystem<ExampleSystem>();
}

// alternative
void func(){
	// initialize ECS

	// register system and get reference to the system
	ExampleSystem_Ptr ex = ECS::RegisterSystem<ExampleSystem>();

	// use reference to system
	ex->do_something();
}
```

To set up the system's signatures using [[ECS]] we need to have registered [[Component]]s to then set up the signatures using "SetSystemSignature()"
```cpp
// example components
struct Health{
	int num;
};

struct Position{
	int x, y;
};

void func(){
	// initialize ECS
	// register components
	// register system

	// create signature
	Signature sig;
	// give the signature the components 
	sig.set(ECS::GetComponentType<Health>());
	sig.set(ECS::GetComponentType<Position>());
	// set the system's signatures
	ECS::SetSystemSignature<ExampleSystem>(sig);
}

// alternative
void func(){
	// initialize ECS
	// register components
	// register system
	
	// set the system's signatures
	ECS::SetSystemSignature<ExampleSystem>(
		ECS::GetComponentType<Health>(),
		ECS::GetComponentType<Position>()
	);
}
```

Within the system we created, to grab the entities which have a matching signature that the [[ECS]] gives and do something with them in a function there is a array called "entities"
```cpp
// define a function in the system
void ExampleSystem::func(){
	// loop through all given entities from ECS
	for(auto const& entity ; entities){
		// grab reference of a entity's component
		
		// do something with the component...
	}
}
```

The following are [[ECS]] functions that concern [[System]] directly

Header location/class name:
```cpp
#include <ecs/ecs.hpp>

class ECS{
...
};
```

## Class functions:
#### public: RegisterSystem<'T'>() -> returns std::shared_ptr<'T'>
* used to register a [[System]] and it as well returns the [[System]] reference for external use
* returns nullptr if the [[System]] is already registered
```cpp
// define system
class MoveSystem : public System{
...
};

void func(){
	// initialize ECS
	// register system
	ECS::RegisterSystem<MoveSystem>();
}

// Alternative
void func(){
	// initialize ECS
	
	// register system and get the system reference
	std::shared_ptr<MoveSystem> system = ECS::RegisterSystem<MoveSystem>();
}
```

#### public: GetSystem<'T'>() -> returns std::shared_ptr<'T'>
* used to get reference of a registered [[System]]
* returns nullptr if the [[System]] hasn't been registered
```cpp
// define system
class MoveSystem : public System{
...
};

void func(){
	// initialize ECS
	// register system
	
	// get registered system
	std::shared_ptr<MoveSystem> system = ECS::GetSystem<MoveSystem>();
}

```

#### public: SetSystemSignature<'T'>(Signature)
* used to set the signatures, the components, that a [[System]] can use for in the entities
* A signature is a set that contains ComponentType
* When setting the signature for the given [[System]], any created entity that contain the same components, it doesn't matter if the entity has more components, that are set in a signature are made available to that [[System]] to access, if the entity doesn't contain the required components they are then removed from that [[System]].
```cpp
// define components
// EX: the following components are used as example
struct Position{
	float x, y;
};

struct Texture{
	int ID;
};

// define system
class MoveSystem : public System{
...
};

void func(){
	// initialize ECS
	// register system

	// create system signature
	Signature sig;
	// give the signature the components 
	sig.set(ECS::GetComponentType<Position>());
	sig.set(ECS::GetComponentType<Texture>());
	// set system signature
	ECS::SetSystemSignature<MoveSystem>(sig);
	// system has access to entities with Position and Texture components...
}

// alternative
void func(){
	// initialize ECS
	// register system

	// set system signature
	ECS::SetSystemSignature<MoveSystem>(
		ECS::GetComponentType<Position>(), 
		ECS::GetComponentType<Texture>()
	);
	// system has access to entities with Position and Texture components...
}
```
