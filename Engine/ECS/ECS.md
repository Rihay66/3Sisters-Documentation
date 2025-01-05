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


[[Entity]]
[[Component]]
[[System]]