The Sound Manager is responsible for handling and managing in setting up audio device, loading sound files, and managing sound sources. It also provides sound buffers (loaded sound files) and sound sources to be access at anytime across the runtime of the application. It is required to have first an audio device setup before doing anything else.

Refer to OpenAL documentation:
* https://www.openal.org/documentation/OpenAL_Programmers_Guide.pdf

### Sound Manager is a Static Singleton Class

To include a Static Singleton Class in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included.

For example: including a Static Singleton Class like [[Sound Manager]]

``` cpp
#include <soundSystems/managers/sound_manager.hpp>
```

From this point the class [[Sound Manager]] calls its private constructor which only initializes as a static object with its private static variables. However, this means that the static functions can be called without having to keep manual storage of the static object.

In C++ to use a static function of a Static Singleton Class can be done as follow:

```cpp
#include <soundSystems/managers/sound_manager.hpp>

void func(){
	// retrieve a loaded sound buffer with the name "jump_sound"
	SoundBuffer buf = SoundManager::GetSound("jump_sound");
}
```


By simply calling the [[Sound Manager]]'s "InitDevice" function it will both initialize OpenAL and setup by default a sound device that your operating system set as being the "default" or "primary"
```cpp
void func(){
	// initialize OpenAL and default OS sound device
	SoundManager::InitDevice();
}
```

It's also possible to select a different sound device before initializing, which is possible to make UI for selecting a sound device. This is done by calling the [[Sound Manager]]'s "GetAllSoundDevices" function to get a current list of connected sound devices.
```cpp
void func(){
	// get list of current sound devices
	std::vector<std::string> devices = SoundManager::GetAllSoundDevices();
	
	//? Do some logic and IU to let user select their sound device
	
	// initialize OpenAL and preferred sound device
	SoundManager::InitDevice(devices[selectedDevice]);
}
```

From this point you can load sound files, **Must be in WAV format**, and due note that sounds are loaded within the [[Sound Manager]]. When loading a file, it can be given a name for easy retrieval. Also note that sounds are loaded as **"SoundBuffers"**
```cpp
void func(){
	// load a sound
	SoundManager::LoadSound("music", "sounds/music.wav");
}
```

Using the name given in the example above being "music", it returns an ID of that sound to which OpenAL uses to play that loaded sound
```cpp
void func(){
	// get sound buffer
	unsigned int musicSoundBuffer = SoundManager::GetSound("music");
	
	// utilize obtained sound buffer to then play it...
}
```

Due note that you can't retrieve a resource or object that is loaded later as it needs to first exist in the very beginning of the application.
```cpp
void func(){
	// get sound buffer called "music", ERROR: "music" doesn't exist yet
	unsigned int musicSoundBuffer = SoundManager::GetSound("music");
	
	// load sound file and have it called "music"
	SoundManager::LoadSound("music", "sounds/music.wav");
	
	// from this point forward, "music" can be retrieved
}
```

Furthermore, [[Sound Source]] can also be created and obtained through [[Sound Manager]], though it's recommended to keep a reference of the sound source to avoid always doing a linear search on every frame. 
```cpp
void func(){
	// create sound source and keep a copy of it
	SoundSource source = SoundManager::CreateSoundSource("music_player");
}
```


Header location/namespace/class name
```cpp
#include <soundSystems/managers/sound_manager.hpp>

class SoundManager{
...
};
```
## Class Functions:
#### static public: InitDevice(const char*) 
* Initializes OpenAL and a given audio device by their explicit name
* By default the OS default selected device is used
* NOTE: It is important to call thins function before calling another function
```cpp
void func(){
	// initialize OpenAL and setup default audio device
	SoundManager::InitDevice();
}
```
#### static public: GetAllSoundDevice(bool) -> returns std::vector<std::string>
* Returns a list of all sound devices connected to the system
* Optional debug printing of the queried device names. It is disabled by default
```cpp
void func(){
	// get list of all sound devices
	std::vector<std::string> devices = SoundManager::GetAllSoundDevices();
	
	// Example: Do some logic to let user pick out their audio device
	
	// initialize OpenAL and setup user specified audio device
	SoundManager::InitDevice(devices.at(userSelection)); 
}
```
#### static public: GetSound(std::string) -> returns SoundBuffer (aka unsigned integer)
* Use to retrieve a loaded sound buffer ID by name
```cpp
void func(){
	// retrieve loaded sound buffer called "music"
	SoundBuffer buf = SoundManager::GetSound("music");
}
```
#### static public: GetSoundSource(std::string) -> returns [[SoundSource]]&
* Used to retrieve a existing [[Sound Source]] by name
```cpp
void func(){
	// retrieve a existing sound source called "music_player"
	SoundSource& source = SoundManager::GetSoundSource("music_player");
}
```
#### static public: LoadSound(std::string, const char*) -> returns SoundBuffer (aka unsigned integer)
* Used to load a sound file to be turned into a buffer with an attached name
* NOTE: sound files must be in **WAV format**
```cpp
void func(){
	// load sound file and have it called "music"
	SoundManager::LoadSound("music", "sounds/music.wav");
	
	// get sound buffer called "music", since it's been loaded prior
	unsigned int musicSoundBuffer = SoundManager::GetSound("music");
}
```
#### static public: CreateSoundSource(std::string, unsigned int) -> returns [[SoundSource]]&
* Creates a [[Sound Source]] with an attached name for retrieval
* Optional: A sound buffer can be given
```cpp
void func(){
	// create a sound source called "player" and give valid sound buffer
	SoundManager::CreateSoundSource("player", soundBuf);
	
	// create a sound source called "music_player" and keep a local reference
	SoundSource source = SoundManager::CreateSoundSource("music_player");
}
```