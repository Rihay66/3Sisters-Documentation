The Resource Systems are responsible for making it easy to load, manage, and retrieve resources that are important to a graphical application.

Due note that those that are tagged with "Requires OpenGL" means that OpenGL needs be initialized before usage and to satisfy this using the engine is through [[Window]]
```cpp
int main()
```

These Resource Systems are divided into three groups:
##### Resource Types
[[Texture]] (Requires OpenGL)
[[Shader]] (Requires OpenGL)
##### Resource Types Extensions:
* ###### Character
	* This type contains the texture coordinates of a particular character from a loaded font which can be used for rendering text
* ###### SubTexture
	* This type contains the texture coordinates of a texture
##### Resource Managers
The resource managers are static singleton classes

[[Resource Manager]] - (Requires OpenGL) handles texture and shader loading and retrieving
[[Tag Manager]] - handles keep track of any arbitrary type using a name

Other types of resource managers
[[Sound Manager]] - handles loading and retrieving sound
[[ECS]] - handles managing entities, components, and systems