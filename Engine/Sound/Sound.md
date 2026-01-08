The Sound is responsible for allowing to load and play audio as well as allowing to manipulate the loaded sound to be louder, have a different pitch, and/or sound like its positioned somewhere in space. The engine utilizes currently OpenAL or OpenAL-Soft back-end that allows for playing sound. 

Refer to OpenAL documentation:
* https://www.openal.org/documentation/OpenAL_Programmers_Guide.pdf

The sound systems use a combination of a Static Singleton, [[Sound Manager]], and objects [[Sound Source]] 

To include a Static Singleton module in a source or header file can be done simply by including their header. Doing multiple includes makes no difference as long as they're included.

For example: including a Static Singleton module like [[Sound Manager]]

``` cpp
#include <soundSystems/managers/source_manager.hpp>
```

From this point the class [[Sound Manager]] calls its private constructor which only initializes as static object with its private static variables.

In C++ to use a function of a Static Singleton class can be done as follow:

```cpp
#include <soundSystems/managers/source_manager.hpp>

void func(){
	// get a sound source
	// StaticClassName::Function()
	SoundSource source = SoundManager::GetSoundSource("player");
}
```

However, some of the Static Singleton Classes require more information to be fully used anywhere else so some may need to be used in sequence.

To get started, the first thing is to setup the hardware of where the sound will play through, like your speakers, headphones, and etc, which can be done through [[Sound Manager]]. 

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

Furthermore, [[Sound Source]] can also be created and obtained through [[Sound Manager]], though it's recommended to keep a reference of the sound source to avoid always doing a linear search on every frame. 
```cpp
void func(){
	// create sound source and keep a copy of it
	SoundSource source = SoundManager::CreateSoundSource("music_player");
}
```

You can also modify the sound source that is inside of the [[Sound Manager]] by either keeping a reference or calling functions directly
```cpp
void func(){
	// modify pitch of sound source directly
	SoundManager::GetSoundSource("music_player").setPitch(0.4f);
	
	// Alternatively, modify pitch by keeping a reference
	SoundSource& source = SoundManager::GetSoundSource("music_player");
	source.setPitch(0.5f);
}
```

To play sounds with a [[Sound Source]] you must give it a **"SoundBuffer"** to play as well you can change the sound buffer later in your application
```cpp
void func(){
	// give sound source a sound buffer upon creation
	SoundManager::CreateSoundSource("music_player", musicSoundBuffer);
	
	// change sound source sound buffer
	SoundSource& source = SoundManager::GetSoundSource("music_player");
	source.setBuffer(newMusicSoundBuffer);
	
	// Alternatively, changing the sound buffer directly
	SoundManager::GetSoundSource("music_player").setBuffer(newMusicSoundBuffer);
}
```

To **play** a sound from a sound buffer, simply call it from the [[Sound Source]]
```cpp
void func(){
	// play sound from a sound source
	SoundSource& source = SoundManager::GetSoundSource("music_player");
	source.play();
	
	// play sound from a sound source directly
	SoundManager::GetSoundSource("music_player").play();
}
```

The sound modules are separated parts that make the sound system of the engine:
* [[Sound Manager]] - keeps track of loaded sounds, sound sources, and can initialize OpenAL to allow for playing audio and can ONLY opens default sound device
* [[Sound Source]] - allows for playing a given sound buffer, can change how the audio is played (EX: different pitch or gain)
