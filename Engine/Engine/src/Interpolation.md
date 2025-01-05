Linear interpolation or named interpolation for short. In the engine it is used to smooth out rendered Physics objects, being translated around the screen.  This can be very helpful for when the a Physics engine runs at a lower frame rate than the renderer as without it it can cause the Physics objects to skip around

Inspired from this resource:
* https://gafferongames.com/post/fix_your_timestep/ 

Within the engine it is optional to utilize Interpolation and much of it is already integrated

Also NOTE that currently is only available for 2D and doesn't include interpolation for rotations

header location/struct
```cpp
#include <engine/components/interpolation.hpp>

struct State{
	float posX;
	float posY;
};

struct Interpolation{
	State current;
	State previous;
}
```
### State
* is a data structure that contains two variables that refer to 2D positions
	* posX -> refers to the X-axis position
	* posY -> refers to the Y-axis position
### Interpolation
* is a data structure that contains two States that refer to two different positions between frames
	* current -> refers to the current frame's 2D position
	* previous -> refers to last frame's 2D position

#### interpolateState(State&, double, State&, State&)
* used to calculate the interpolated state between previous and current states
* requires the calculated alpha between every frame
## Remember that the position of Interpolation is completely separate from the position of the object you pair interpolation to, thus you must update both positions

##### Here's an example on how to utilize the current interpolation design which should explain how its used within the engine:

In the example we'll use GLM to use vectors:

Refer to GLM documentation: 
* http://glm.g-truc.net/0.9.9/api/modules.html

First off, initialize an object called player that has a position and the interpolation struct
```cpp
// for this example well call it "Player"
struct Player{
	// 2D position of the player
	glm::vec2 position;
	// interpolation state
	Interpolation inter;
}
```

Then we'll utilize the [[Window]] paradigm to initialize our objects as the class does calculations that helps us use interpolation for every frame and passes the result into the parameter of render() which is called "alpha"

Then we'll move the player to the right and update the interpolation within stepUpdate(), because stepUpdate always runs at the specified frame time by the variable called FixexTimeStep, further info can be found on [[Window]]

Finally, we calculate the interpolated position using interpolateState() which then can be used for rendering

```cpp
void init(){
	// initialize Player and the interpolation's states

	// start player at zero
	player.position = glm::vec2(0.0f);

	// initialize interpolation to player's position
	player.inter.current.posX = player.position.x;
	player.inter.current.posY = player.position.y;
	player.inter.previous.posX = player.position.x;
	player.inter.previous.posY = player.position.y;
}

// this function is usually used to update Physics
void stepUpdate(){
	// move the player and then update the interpolation

	// move player right every frame
	player.position += glm::vec2(1.0f, 0.0f);

	// update interpolation

	// set the interpolation previous state to current
	player.inter.previous.posX = player.inter.current.posX;
	player.inter.previous.posY = player.inter.current.posY;

	// set interpolation current state equal to the player's position
	player.inter.current.posX = player.position.x;
	player.inter.current.posY = player.position.y;
}

void render(double alpha){
	// render the player

	// get the interpolation between states
	Interpolation interpolation;
	/* 
	get the interpolated state between the previous and 
	current position of the player
	*/
	interpolateState(interpolation, alpha,
	 player.inter.previous, player.inter.current);

	// render the player with interpolation
}
```
