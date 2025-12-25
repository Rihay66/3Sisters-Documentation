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

Within Resource Systems there are managers that well... manage resources and types that are specific to a graphical application or program. Before heading into learning what each manager or type does, you should know how to utilize Static Singleton Classes which you can find here [[3 Sisters-Framework-Engine]] as any manager that manages any kind of resource is typically a Static Singleton Class unless stated otherwise.
##### Resource Types
[[Texture]] - (Requires OpenGL) handles generating a texture given data that is extracted from an image file
[[Shader]] - (Requires OpenGL) handles compiling shader code given from shader files such as a vertex, fragment, or geometry shader. It also contains functions to change values of the given shader

These resource types are Classes that handle doing the calculation, generating, or modifying either texture or shader usable to OpenGL.
##### Resource Types Extension:
* ###### CharacterSet
	* This type contains the texture coordinates of all particular characters from a loaded font which can be used for rendering text
* ###### SubTexture
	* This type contains the texture coordinates of a texture

These resource types extensions mean that they extend the use of a certain resource type and the ones shown extend from [[Texture]] indirectly as they require just the texture ID that is generated.
##### Resource Managers
[[Texture Manager]] - (Requires OpenGL)  handles [[Texture]], Font, and SubTexture loading and retrieving 
[[Shader Manager]] - (Requires OpenGL) handles [[Shader]] loading and retrieving

Within these managers they manage resources by having them be pointing to a given name and once set it then can be used for retrieving the desired resource. 
```cpp
void func(){
	// load and generate a texture from file and give it a name
	TextureManager::LoadTexture("cat.png", "Pet");
}
```

From the example above, we loaded a texture called "cat.png" and gave it a name of "Pet", so within the manager it generated a texture that points to the name "Pet". 

Now to retrieve the generated texture
```cpp
void func(){
	// retrieve the texture index to apply to a quad
	int textureIndex = TextureManager::GetTextureIndex("Pet");
	
	// utilize retrieved texture for rendering...
}
```

Most managers that manage resources utilize this system of storing and retrieving stuff from just using a name and due note that name is explicit meaning that "Pet" != "pet".

When compiling your application into an executable program and upon opening you are given an error that the program can't find a file, as an example we'll use a texture file called "cat.png", and the program gives an error. 

This is because the program wasn't either given a path to the file and/or the file is not within reach of the executable. To properly set this up or fix the issue follow the following:

In the directory of where the application was compiled to should look likes this:

	/build
		/shaders
		/textures
			cat.png
		myapp.exe

In the example above, the executable is within a "build" folder and along side with two other folders "shaders" and "textures". Within the "textures" folder is the texture the program is trying to load is called "cat.png".

In the code of the program the line that's giving the error
```cpp
TextureManager::LoadTexture("cat.png","cat");
```

#### There are two ways to fix this:
##### Move the file "cat.png" outside of the "textures" folder and next to the executable so the build folder so it should look like this
	/build
		/shaders
		/textures
		cat.png
		myapp.exe
Now the program now properly executes, but by organization of the files can get messy and likely to cause a headache in finding what files is missing when the program gets larger.

##### Change the line of code that loads the texture to be given a path, so the code would look likes this
```cpp	
TextureManager::LoadTexture("textures/cat.png", "cat");
```
	
Now the code properly executes and the organization of the files is much better as the program gets larger we can separate what files are "textures", "shaders", "sound files", or etc.

Here are all the Resource systems for quick reference:
[[Texture]]
[[Shader]]
[[Texture Manager]]
[[Shader Manager]]

Other types of resource managers (these don't require OpenGL or a window context)
[[Sound Manager]] - handles loading and retrieving sound
[[ECS]] - handles managing entities, components, and systems