To understand the engine let's understand how what it contains and how it operates:

this is the include header file tree:
```
inc/
	cameras/
	ecs/
		types/
		systems/ <- Pre-constructed ECS systems
		managers/
		components/ <- Pre-defined ECS components
	engine/
		components/
	glad/ <- Cross-platform OpenGL API
	input/
		managers/
	resourceSystems/
		managers/
		types/
	sound/
		managers/
	stb/ <- Image loading STB
	window/
```

# Static-Singletons
Resource/Functional modules of the engine are Static Singletons Classes where their resources are stored statically and automatically freed without any explicit function call.

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included.

For example: including a Static Singleton Class like [[Resource Manager]]

``` cpp
#include <ResourceSystems/Managers/ResourceManager.hpp>
```

From this point the class [[Resource Manager]] calls its private constructor which only initializes as a static object with its private static variables.

In C++ to use a function of a Static Singleton Class can be done as follow:

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
	// load a shader
	// apply projection calculation to the shader
	
	// initialize sprite renderer with a shader and a custom 2D sprite size 
	SpriteRenderer::Init(shader, {15.0f, 60.0f});
}
```

Throughout the Documentation of this engine, each the module that are Static Singleton Classes are labelled.

For quick reference, here are the Static Singleton Classes in the engine:
* [[Resource Manager]]
* [[Sprite Renderer]]
* [[Text Renderer]]
* [[Sound Manager]]
* [[ECS]]
* [[GLFW Gamepad]]
* [[GLFW keyboard]]
* [[SDL Gamepad]]
* [[SDL keyboard]]
# ECS
The Entity Component System ([[ECS]]) is a different approach to representing objects or "GameObjects" and using them to keep track and allow for some functionality. Despite its performance benefits, though its design and usage requires a lot more thinking and even getting used to if you've been used to OOP. This general information should give enough information to start thinking about and not only that there will be examples and tutorials showing its usage and application.

[[ECS]] is a Static Singleton Class meaning it needs to be initialized before use
```cpp
#include <ecs/ecs.hpp>

void func(){
	// initialize ECS
	ECS::Init();
}
```

### [[Entity]]
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

To create an Entity in code it would look like so
``` cpp
#include <ecs/ecs.hpp>

void func(){
	// initialize ECS
	
	// create an entity
	ECS::CreateEntity();
}
```

Since the "CreateEntity()" returns an entity which can be used to keep track of that entity
``` cpp
#include <ecs/ecs.hpp>

void func(){
	// create an entity
	Entity entity = ECS::CreateEntity();
	
	// do something with 'entity'
	// ...
}
```
### [[Component]]
A Component is represented as a data structure that only contains data. So to create a component it would look likes this:

```cpp
// example component with data
struct Component{
	int value;
	float timer;
};
```

These components can be attached to an entity or numerous entities. Each entity can have the same component but they'll have a different instance of that same component, meaning similar components attached to different entities don't affect each other when a change only happens to one component or entity.

In the [[ECS]] the component that going to be used must be registered in order for the components to be used and [[System]]s to work properly, can be done so by using "RegisterComponent<>()"

```cpp
#include <ecs/ecs.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

void func(){
	// initialize ECS
	
	// register component
	ECS::RegisterComponent<ComponentExample>();
}
```

To attach a component to a entity using "AddComponent()"
```cpp
#include <ecs/ecs.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

void func(){
	// initialize ECS
	// register components
	
	// create an entity
	Entity entity = ECS::CreateEntity();
	
	// attach component to entity
	ECS::AddComponent(entity, ComponentExample{});
}
```

It is also possible to set values of a component right when adding the component to the entity
```cpp
#include <ecs/ecs.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

void func(){
	// initialize ECS
	// register components
	
	// create an entity
	Entity entity = ECS::CreateEntity();
	
	// attach component to entity
	ECS::AddComponent(entity, ComponentExample{.value = 10, .size = 2.0f});
}
```

When adding a component to an entity it's added to an array in the "ComponentArray" which is just a list of components a entity is attached to and as more components are added it'll continue to fill each of these spots. This is useful for [[System]]s because entities with certain components can be accessed by systems and entities with either one missing component or none at all aren't accessed as they are missing the base requirements.

Due note that entities have a maximum amount of components they can have as well duplicate of the same components added or removed to an entity causes an error message but no compilation or runtime error occurs. 

To remove a component using "RemoveComponent()"
```cpp
#include <ecs/ecs.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

void func(){
	// initialize ECS
	// register components
	
	// create an entity
	Entity entity = ECS::CreateEntity();
	
	// attach component to entity
	ECS::AddComponent(entity, ComponentExample{});
	
	// remove component from entity
	ECS::RemoveComponent<ComponentExample>(entity);
}
```

There the obvious note that removing the component of an entity, the values set to that component of the entity is disposed of and lost. 

In similar fashion the "ComponentArray" attached to the entity also changes, meaning Systems with requiring a specific set of components and the entity now has one missing required component, the system will dispose access to that entity.

To access component of an entity there are many ways to grab and change things of the component, here's a few examples of that using "GetComponent()"

```cpp
#include <ecs/ecs.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

void func(){
	// initialize ECS
	// register components
	// create an entity
	// attach ComponentExample to entity
	
	// take reference of the component in Entity
	ComponentExample& component = ECS::GetComponent<ComponentExample>(entity);
	
	// modify component of the entity
	component.value += 30;
} 
```

```cpp
#include <ecs/ecs.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

void func(){
	// initialize ECS
	// register components
	// create an entity
	// attach ComponentExample to entity
	
	// get component from entity and modify directly an attribute
	ECS::GetComponent<ComponentExample>(entity).size -= 1.0f;
} 
```
### [[System]]
A System is sort of a manager that is able to access all entities that have a set of specific components that is required by the system in order to manipulate the entities.

Systems require to be registered before usage utilizing "RegisterSystem()", this time it is required to keep a reference to the return of the function in order to use the functions provided by the system being used
```cpp
#include <ecs/ecs.hpp>

// utilize existing ECS system
#include <ecs/systems/ecs_sprite_renderer.hpp>

void func(){
	// initialize ECS
	
	// register system
	ECS_SpriteRendererPtr renderer = ECS::RegisterSystem<ECS_SpriteRenderer>();
}
```

When entities are given a component it'll not only attach the component to the entity but also fill a spot in its "ComponentArray" which is used to be compared to the System's "Signature". Signatures is a list of required components needed for an entity to be access by a System. 

There are two ways to set up the signatures of a system all using the same function "SetSystemSignature()":

Creating a signature and adding it to the system
```cpp
#include <ecs/ecs.hpp>

// utilize existing ECS system
#include <ecs/systems/ecs_sprite_renderer.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

// example component with data
struct TestComponent{
	double length;
};

void func() {
	// initialize ECS
	// register components
	// register system
	
	// create signature
	Signature sig;
	// give the signature the components 
	sig.set(ECS::GetComponentType<ComponentExample>());
	sig.set(ECS::GetComponentType<TestComponent>());
	// set the system's signatures
	ECS::SetSystemSignature<ECS_SpriteRenderer>(sig);

	// use system
	renderer.render(ts);
}
```

Setting the signature directly onto function
```cpp
#include <ecs/ecs.hpp>

// utilize existing ECS system
#include <ecs/systems/ecs_sprite_renderer.hpp>

// example component with data
struct ComponentExample{
	int value;
	float size;
};

// example component with data
struct TestComponent{
	double length;
};

void func() {
	// initialize ECS
	// register components
	// register system
	
	// set the system's signatures
	ECS::SetSystemSignature<ECS_SpriteRenderer>(
		ECS::GetComponentType<ComponentExample>(), 
		ECS::GetComponentType<TestComponent>());
	
	// use system
	renderer.render(ts);
}
```
# Inheritance and Virtual Functions
The following example modules are the classes that either optional or need to be inherited and its functions overwritten to allow for some or further functionality.
# [[Window]] 
The Window class is responsible for creating a OpenGL context window which allows for direct/indirect functionality of other components within the engine such as the [[Sprite Renderer]]. 

Not only does this class provide a OpenGL context window it also provides a runtime function that keeps the context window open, provide runtime functions like the "update()" and "Render()"

Furthermore, this class is intended and meant to be inherited because the functionality it provides needs to be arbitrarily defined, however there are some functions that can be optionally overridden to change their functionality to fulfill some purpose or need. 

For example, when defining a what happens every frame we need to override "Update()" that is in the [[Window]] class and give it a definition
```cpp
void ExampleWindow::Update(){
	// update logical systems every frame
}
```

The following classes that NEED to be overridden:
* Init()
* Update()
* StepUpdate()
* Input()
* Render()
# [[Camera]] 
The Camera class allows for keeping track and provide calculation of projection and within this class it is optional to override the how the projection is calculated which is called "calculateProjectionView()"

# Namespaces
Within the Engine there is support for two different libraries that are for windowing and input which are:
* GLFW
* SDL
To allow for almost seamless transition between switching these libraries engine has its own unique namespace that pertains to them

For example, defining a window class utilizing GLFW windowing
```cpp
// take note of the included header
#include <window/glfw_window.hpp>

// use the GLFW namespace
using namespace GLFW;

class AppWindow : public Window{
	// define the class...
};
```

Now, for a reasonable reason you would like to switch to SDL, here how its done for this peculiar example
```cpp
// take note of the included header
#include <window/sdl_window.hpp>

// use the SDL namespace
using namespace SDL;

class AppWindow : public Window{
	// define the class...
};

```

However, you do need to look out for any differences between the two when it comes down to migrating from one to another as there can be some differences in missing feature or a function requires additional initializing variables or parameters. 

Upon reaching at this point, you should understand what the engine contains and how its ecosystem works. Now you can move onto understanding how each module operates, the list below will be listed in order of importance before heading into [[Usage]] which there we learn how to set up and make a simple game utilizing most the modules of the engine: 
* [[Window]]
* [[Resource Systems]]
* [[ECS]]
* [[Camera]]
* [[Engine]]
* [[Input]]
* [[Sound]]