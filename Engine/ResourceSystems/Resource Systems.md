The Resource Systems are responsible for making it easy to load, manage, and retrieve resources that are important to a graphical application.

Due note that those that are tagged with "Requires OpenGL" means that OpenGL needs be initialized before usage and to satisfy this using the engine is through [[Window]]
```cpp
int main(){
	// create App window class that inherits from Window
	AppWindow app;
	
	// initialize window
	app.initializeWindow(800, 600, "My App");
	
	// At this point Resource system that require OpenGL are satisfied...
}
```

Within Resource Systems there are managers that well... manage resources and types that are specific to a graphical application or program. Before heading into learning what each manager or type does, you should know how to utilize Static Singleton Classes which you can find here [[3 Sisters-Engine]] as any manager that manages any kind of resource is typically a Static Singleton Class unless stated otherwise.
##### Resource Types
[[Texture]] - (Requires OpenGL) handles generating a texture given data that is extracted from an image file
[[Shader]] - (Requires OpenGL) handles compiling shader code given from shader files such as a vertex, fragment, or geometry shader. It also contains functions to change values of the given shader

These resource types are Classes that handle doing the calculation, generating, or modifying either texture or shader usable to OpenGL.
##### Resource Types Extension:
* ###### Character
	* This type contains the texture coordinates of a particular character from a loaded font which can be used for rendering text
* ###### SubTexture
	* This type contains the texture coordinates of a texture

These resource types extensions mean that they extend the use of a certain resource type and the ones shown extend from [[Texture]] indirectly as they require just the texture ID that is generated.
##### Resource Manager
[[Resource Manager]] - (Requires OpenGL)  handles [[Texture]], [[Shader]], Character, and SubTexture loading and retrieving 

Within the resource manager it manages resources by having them be pointing to a given name and once set it then can be used for retrieving the desired resource. 
```cpp
void func(){
	// load and generate a texture from file and give it a name
	ResourceManager::LoadTexture("cat.png", "Pet");
}
```

From the example above, we loaded a texture called "cat.png" and gave it a name of "Pet", so within the manager it generated a texture that points to the name "Pet". 

Now to retrieve the generated texture
```cpp
void func(){
	// retrieve the texture index to apply to a quad
	int textureIndex = ResourceManager::GetTextureIndex("Pet");
	
	// utilize retrieved texture for rendering...
}
```

Most managers that manage resources utilize this system of storing and retrieving stuff from just using a name and due note that name is explicit meaning that "Pet" != "pet"

*In the future there will be more different types of Resource managers*

Here are all the Resource systems for quick reference:
[[Texture]]
[[Shader]]
[[Resource Systems]]

Other types of resource managers (these don't require OpenGL or a window context)
[[Sound Manager]] - handles loading and retrieving sound
[[Tag Manager]] - handles keep track of any arbitrary type 
[[ECS]] - handles managing entities, components, and systems