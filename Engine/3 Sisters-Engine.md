To understand the engine let's understand how it operates:
(explain what it mainly uses: static singletons, ECS, and inheritance, and virtual functions)

# Static-Singletons
Resource/Functional modules of the engine are Static Singletons Classes where their resources are stored statically and automatically freed without any explicit function call.

To include a Static Singleton component in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as their included.

For example: including a Static Singleton component like [[Resource Manager]]

``` cpp
#include <ResourceSystems/Managers/ResourceManager.hpp>
```

From this point the class [[Resource Manager]] calls its private constructor which only initializes as static object with its private static variables.

In C++ to use a function of a Static Singleton class can be done as follow:

```cpp
#include <resourcesystems/managers/resource_manager.hpp>

void func(){
	// rebind all textures to be used by OpenGL
	ResourceManager::BindTextures();
}
```

However, some of the Static Singleton Classes require more information to be fully used anywhere else so some may need to be used in sequence.

For example: [[Sprite Renderer]] requires a loaded [[Shader]] and optionally a set universal sprite size

``` cpp
#include <engine/sprite_renderer.hpp>

void func(){
	//... load a shader
	
	// initialize sprite renderer with a shader and a custom 2D sprite size 
	SpriteRenderer::Init(shader, {15.0f, 60.0f});
}

```

Throughout the Documentation of this engine, each the components that are Static Singleton Classes are labelled.

For quick reference, here are the Static Singleton Classes in the engine:
* [[Resource Manager]]
* [[Tag System]]
* [[Sprite Renderer]]
* [[Text Renderer]]
* [[Sound Manager]]
* [[Physics]]
* [[ECS]]
* [[GLFW Gamepad]]
* [[GLFW keyboard]]
# ECS
The Entity Component System ([[ECS]]) is a different approach to representing objects or "GameObjects" and using them to keep track and allow for some functionality. Despite its performance benefits, though its design and usage requires a lot more thinking and even getting used to if you've been used to OOP. This general information should give enough information to start thinking about and not only that there will be examples and tutorials showing its usage.

### Entity
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

To create an Entity in code it would look like so:
``` cpp
#include <ecs/ecs.hpp>

void func(){
	// create an entity
	ECS::CreateEntity();
}
```

Since the "CreateEntity()" returns an entity which can be used to keep track of that entity
``` cpp
#include <ecs/ecs.hpp>

void func(){
	// create an entity
	Entity e = ECS::CreateEntity();
	
	// do something with 'e'
	// ...
}
```
### Component
A Component is represented as a data structure that only contains variables. So to create a component it would look likes this:

```cpp
// example component with variables
struct component{
	int value;
	float timer;
};
```

These data structures can be attached to entities which can then be used to represent the data of an entity (rework the wording on this)
### System

### Putting it all together

# Inheritance and Virtual Functions

# [[Window]] 
This component allows for direct/indirect functionality of other components within the engine such as 
# [[Cameras]]
# [[Engine]]
# [[ECS]]
# [[Resource Systems]]
# [[Engine/Sound/Sound]] 
# [[Input]]

After examining and going over each of the components of the engine, you should be ready to start