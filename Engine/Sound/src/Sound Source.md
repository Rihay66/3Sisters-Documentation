The Sound Source is responsible for playing a given sound buffer (ei a loaded sound file by [[Sound Manager]]). The modifiable properties  when playing a sound can be gain, pitch, and position in 3D space. It is required to have first an audio device setup before doing anything else through OpenAL.

Refer to OpenAL documentation:
* https://www.openal.org/documentation/OpenAL_Programmers_Guide.pd

Before utilizing [[SoundSource]] ensure to initialized [[Sound Manager]]  by simply calling the [[Sound Manager]]'s "InitDevice" function it will both initialize OpenAL and setup by default a sound device that your operating system set as being the "default" or "primary"
```cpp
void func(){
	// initialize OpenAL and default OS sound device
	SoundManager::InitDevice();
}
```
 Further details on initializing can be found in the [[Sound Manager]]

It **recommended** to create [[Sound Source]] objects through [[Sound Manager]]. However, you can create sources separately though **YOU MUST** handle OpenAL cleanup yourself. You must call to "generateSource" function in order to utilize any other function in [[Sound Source]]
```cpp
void func(){
	// creating an empty sound source obj
	SoundSource source;
	
	// generate sound source
	source.generateSource();
	
	// OpenAL destroy sound source
    alDeleteSources(1, &source.getSource());
}
```

Before playing a loaded sound buffer, due note that they can be loaded through [[Sound Manager]]. [[Sound Source]] is flexible when it comes to playing a desired sound, so there are multiple different methods.

**Due note a single sound source can only play ONE sound at a time**

For the following examples assume that you have a loaded sound through [[Sound Manager]] and only creating [[Sound Source]] by hand rather than using [[Sound Manager]].

It doesn't matter when the "play" function is being called multiple times as it just continues playing until it is done. 

Method 1: Giving a sound buffer after creation and playing sound
```cpp
void func(){
	//? Assume loaded sound buffer has name of "music"
	
	// creating an empty sound source obj
	SoundSource source;
	
	// generate sound source
	source.generateSource();
	
	// setting the sound buffer 
	source.setBuffer(SoundManager::GetSound("music"));
	
	// playing prior loaded sound buffer called "music"
	source.play();
	
	// OpenAL destroy sound source
    alDeleteSources(1, &source.getSource());
}
```

Method 2: Giving sound buffer when playing, **due note this replaces the current loaded sound buffer**
```cpp
void func(){
	//? Assume loaded sound buffer has name of "music"
	
	// creating an empty sound source obj
	SoundSource source;
	
	// generate sound source
	source.generateSource();
	
	// giving a sound buffer and then playing it
	source.play(SoundManager::GetSound("music"));
	
	// OpenAL destroy sound source
    alDeleteSources(1, &source.getSource());
}
```

To stop playing sound utilize the "stop" function
```cpp
void func(){
	//? Assume loaded sound buffer has name of "music"
	
	// creating an empty sound source obj
	SoundSource source;
	
	// generate sound source
	source.generateSource();
	
	// giving a sound buffer and then playing it
	source.play(SoundManager::GetSound("music"));
	
	// stopping sound being played
	source.stop();
	
	// OpenAL destroy sound source
    alDeleteSources(1, &source.getSource());
}
```

Header location/namespace/class name
```cpp
#include <soundSystems/sound_source.hpp>

class SoundSource{
...
};
```
## Class Functions:
#### public: generateSource() -> returns unsigned int&
* Used to create a OpenAL instance of a sound source
* Returns OpenAL source ID
* [[Sound Manager]] typically handles create sound sources
* Note: you must handle source deletion yourself
```cpp
void func(){
	// creating an empty sound source obj
	SoundSource source;
	
	// generate sound source and keep a reference
	unsigned int& id = source.generateSource();
	
	// OpenAL destroy sound source by o
    alDeleteSources(1, &id));
}
```

#### public: setPitch(float)
* Used to change the pitch of the sound to be played
* By default pitch is set to 1.0f
* Given range must be within 0.0f to 1.0f
```cpp
void func(){
	//? Assume sound source is already created
	
	// change the pitch of the sound
	source.setPitch(0.8f);
	
	// play sound...
}
```

#### public: setGain(float)
* Used to change the gain or loudness of the sound to be played
* By default gain is set to 1.0f (which is max volume)
* Given range must be within 0.0f to 1.0f
```cpp
void func(){
	//? Assume sound source is already created
	
	// change the gain of the sound
	source.setGain(0.8f);* 
	
	// play sound...
}
```
#### public: setPosition3D(glm::vec3)
* Used to set the position of the source in 3D space
* By default source sits at center, or in other words sits right in front of the camera
```cpp
void func(){
	//? Assume sound source is already created
	
	// change the position of the sound above
	source.setPosition3D(glm::vec3(0.0f, 1.0f, 0.0f));
	
	// play sound...
}
```
#### public: setPosition2D(glm::vec2)
* Used to set the position of the source in 2D space
* By default source sits at center, or in other words sits right in front of the camera
```cpp
void func(){
	//? Assume sound source is already created
	
	// change the position of the sound above
	source.setPosition2D(glm::vec2(0.0f, 1.0f));
	
	// play sound...
}
```

#### public: setBuffer(unsigned int)
* Adds and sets the sound buffer of the sound source
* Sound buffer can be obtained from [[Sound Manager]]
```cpp
void func(){
	//? Assume sound source is already created
	
	// set the sound buffer
	source.setBuffer(buffer);
	
	// play sound...
}
```
#### public: setLoopSound(bool)
* sets the "loop sound" parameter to make the sound being played automatically be looped without having to call the "play" function again
```cpp
void func(){
	//? Assume sound source is already created
	
	// set sound to loop
	source.setLoopSound(true);
	
	// play sound...
}
```
#### public: getBuffer() -> returns  unsigned int
* Used to retrieve current buffer loaded into the sound source
```cpp
void func(){
	//? Assume sound source is already created
	
	// get sound buffer
	unsigned int buf = source.getBuffer();
}
```
#### public: getSource() -> returns unsigned int&
* Returns reference of the OpenAL sound source ID
```cpp
void func(){
	//? Assume sound source is already created
	
	// set sound to loop
	unsigned int& src = source.getSource();
	
	// OpenAL destroy sound source
    alDeleteSources(1, &src;
}
```

#### public: play(unsigned int, bool)
* Used to play a sound
* Optional: "playAgainWhenFinished" means to wait for sound to finish to then play again
```cpp
void func(){
	//? Assume sound source is already created
	
	// play sound with "playAgainWhenFinished" being set
	source.play(true);
}

void func(){
	//? Assume sound source is already created
	
	// set sound buffer and play sound with "playAgainWhenFinished" being set
	source.play(buffer, true);
}
```

#### public: stop()
* Used to stop current sound
```cpp
void func(){
	//? Assume sound source is already created
	
	// play sound with "playAgainWhenFinished" being set
	source.play(true);
	
	// stop sound
	source.stop();
}
```

#### public: restartSound()
* Stops current sound and restarts the sound to the beggining
```cpp
void func(){
	//? Assume sound source is already created
	
	// play sound with "playAgainWhenFinished" being set
	source.play(true);
	
	// pause and restart sound
	source.restartSound();
	
	// play again from the beggining
	source.play);
}
```