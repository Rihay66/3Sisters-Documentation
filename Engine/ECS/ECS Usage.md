This page will shows an example on how to use the [[ECS]] module, please refer to [[ECS]] before continuing reading this usage example

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
// this can be it own header which will be called "player_component.hpp"

// representation of the Player's position
struct Position{
	float x; // x-axis position
	float y; // y-axis position
};
```

Now we then register the component to allow for usability of it within ECS
```cpp
#include <ecs/ecs.hpp>
// include the define component defined above
#include "player_component.hpp"

void func(){
	// initialize ECS for app usage
	ECS::Init();

	// register components
	ECS::RegisterComponent<Position>();
}
```

For moving the Player, we need to define a [[System]] where the [[ECS]] which will automatically handle grabbing the player, then the [[System]] we defined can get the component that is attached to the Player, then update the player transform. We can also define *alias* that refers to the [[System]]

So let's create such [[System]]. 

```cpp
// header file named "move_system.hpp"

// include system to create our own
#include <ecs/types/system.hpp

// inherit from System to classify this to the ECS that this is a system
// also gives access to "entities" list which are entities given by ECS that 
// meet the required signature
class MoveSystem : public System{
	public:
		// Move function
		void Move();
};

// define alias
using MoveSystem_Ptr = std::shared_ptr<MoveSystem>;

// source file
#include "move_system.hpp"
// ECS to allow for grabbing components of entities
#include <ecs/ecs.hpp>
// defined component defined above
#include "player_component.hpp"

void MoveSystem::Move(){
	// grab all entities with the set signature
	for(auto const& entity : entities){
		// grab reference of "Position" component from entity
		auto& pos = ECS::GetComponent<Position>(entitiy);

		// move the position on x
		if(*insert input checking code here*){
			pos.x += 1.0f;
		}
	}
}
```

Then we need to register the [[System]] we just created so we can then use the system and allow [[ECS]] to automatically give a list of entities that have the components the [[System]] needs which is the "Position" component
```cpp
#include <ecs/ecs.hpp>
// include the component defined above
#include "player_component.hpp"
// include the system defined above
#include "move_system.hpp"

void func(){
	// initialize ECS for app usage
	// register components

	// register system and get reference of the system
	MoveSystem_Ptr moveSys = ECS::RegisterSystem<MoveSystem>();
}
```

To let [[ECS]] give the entities that have the "Position" component to the [[System]] we created we need to define a signature or a list of components that a [[Entity]] needs to satisfy in order to be included into the [[System]]
```cpp
#include <ecs/ecs.hpp>
// include the component defined above
#include "player_component.hpp"
// include the system defined above
#include "move_system.hpp"

void func(){
	// initialize ECS for app usage
	// register components
	// register system and get reference of the system

	// create signature
	Signature sig;
	// give the signature the components 
	sig.set(ECS::GetComponentType<Position>());
	// set the system's signature
	ECS::SetSystemSignature<MoveSystem>(sig);
}

// alternative
void func(){
	// initialize ECS for app usage
	// register components
	// register system and get reference of the system

	// set the system's signature
	ECS::SetSystemSignature<MoveSystem>(
		ECS::GetComponentType<Position>()
	);
}
```

Now we need a Player [[Entity]] with the component "Position" and to the component set default values

```cpp
#include <ecs/ecs.hpp>
// include the component defined above
#include "player_component.hpp"
// include the system defined above
#include "move_system.hpp"

void func(){
	// initialize ECS for app usage
	// register components
	// register system and get reference of the system
	// set the system's signature

	// create entity
	Entity Player = ECS::CreateEntity();
	// give component to entity
	ECS::AddComponent(Player, Position{.x = 0.0f, .y = 0.0f});
}
```

Lastly, we need to call the function of [[System]] called "MoveSystem" to update the Player [[Entity]] so it can move to the right
```cpp
#include <ecs/ecs.hpp>
// include the component defined above
#include "player_component.hpp"
// include the system defined above
#include "move_system.hpp"

void func(){
	// initialize ECS for app usage
	// register components
	// register system and get reference of the system
	// set the system's signature
	// create entity
	// give component to entity

	// update entities with system reference
	moveSys->Move();
}
```

That should be pretty much it for the [[ECS]] in using from scratch, look into [[Tutorial]] on actual application of the [[ECS]] to create a simple game using this engine.