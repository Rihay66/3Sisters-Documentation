The Window is responsible for creating a OpenGL context window which allows for direct/indirect functionality of other components within the engine such as the [[Quad Renderer]]. 

The Window class is an abstract class meaning YOU HAVE TO inherit this class which means creating a class that inherits from Window in order to make use of it.

For example App class inherits from Window
```cpp
// app.hpp
// recommend to place classes in headers and define functions in source

// since both GLFW and SDL versions the Window class is named the same way
// to differentiate this namespace usage is required
// example: use the GLFW window version module 
using namespace GLFW;

class App : public Window{
	private:
	
	public:
		// constructor
		App();
};
```
Inside the class there are functions that are required to be overridden which then allows for specifying your own code to make the app do something and those functions or paradigm pattern is:
* **init()
* **stepUpdate(double)
* **update()
* **render(double)

\*these functions or paradigm are the same for both GLFW and SDL versions of window*

So it should look like this:

Header
```cpp
// app.hpp

class App : public Window{
	private:
	
	public:
		// constructor
		App();
		// override window functions
		void init() override;
		void stepUpdate(double ts) override;
		void update() override;
		void render(double ts) override;	
};
```

Source
```cpp
#include <app.hpp>

// define constructor, also call Window constructor
App::App() : Window(){

}

// define window functions

void App::init(){
	// initialze resources, players, or systems
}

void App::stepUpdate(double ts){
	// fixed update
}

void App::update(){
	// update
}

void App::render(double ts){
	// render something
}
```

Since the window class is the basis for engine, in order to start using it we need understand the following functions:
* ##### initializeWindow(int, int, const char*)
	* This function initializes the window and OpenGL capabilities. For parameters the function requires width and height of the window being created. Optionally you can give the window a name.
* ##### runtime()
	- This function first calls the function init() and then updates the following functions in a infinite loop until given a close window call
		* init()
		* stepUpdate(double)
		* update()
		* render(double)

To put it all together in a main function
```cpp
#include <app.hpp>

int main(){
	// initialize class obj
	App app;
	
	// initialize the window with a size of 800x600 with the name "My App"
	app.initializeWindow(800, 600, "My App");
	
	// call runtime
	app.runtime();
	
	return 0;
}
```

Now when compiling and executing the executable it should give you a black screen window. 

Here are the currently different libraries that handle windowing with OpenGL capabilities, there are some minor differences between them, but most functions are relatively the same as well with their naming as well:
* **[[Window-GLFW]]
* **[[Window-SDL]] 