The Physics is responsible for simulating created physics objects that are composed of the following [[Component]]s:
* Transform
* BoxCollider <- in the future there will be support for circular collider 
* Rigidbody
* Interpolation
The Physics doesn't handle nor contains any shortcut to apply changes directly to the physics objects, however applying changes to the Rigidbody's runtime id and then have the physics update for the changes to take effect. The Physics extends from Box2D.

Refer to Box2D documentation before continuing
* https://box2d.org/documentation/ 

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

When creating Physics objects