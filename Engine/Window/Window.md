The Window is responsible for creating a OpenGL context window which allows for direct/indirect functionality of other components within the engine such as the [[Quad Renderer]]. 

The Window class is an abstract class meaning YOU HAVE TO inherit this class which means creating a class that inherits from Window in order to make use of it.

For example AppWindow class inherits from Window
```cpp
// app_window.hpp
// recommend to place classes in headers and define functions in source

// since both GLFW and SDL versions the Window class is named the same way
// to differentiate use of namespace is required
// example: use the GLFW window version 
using namespace GLFW;

class AppWindow : public Window{
	private:
	
	public:
		// constructor
		AppWindow();
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
// app_window.hpp

class AppWindow : public Window{
	private:
	
	public:
		// constructor
		AppWindow();
		// override window functions
		void init() override;
		void stepUpdate(double ts) override;
		void update() override;
		void render(double ts) override;	
};
```

Source
```cpp
#include "app_window.hpp"

// define constructor, also call Window constructor
AppWindow::AppWindow() : Window(){

}

// define window functions

void AppWindow::init(){
	// initialize resources, players, or systems
}

void AppWindow::stepUpdate(double ts){
	// fixed update
}

void AppWindow::update(){
	// update
}

void AppWindow::render(double ts){
	// render cool stuff 
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
#include "app_window.hpp"

int main(){
	// initialize class obj
	AppWindow app;
	
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