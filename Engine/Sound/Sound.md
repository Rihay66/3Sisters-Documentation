The Sound is responsible for allowing to load and play audio as well as allowing to manipulate the loaded sound to be louder, have a different pitch, and/or sound like its positioned somewhere in space. The engine utilizes currently OpenAL or OpenAL-Soft backend that allows for playing sound. 

Refer to OpenAL documentation:
* https://www.openal.org/documentation/OpenAL_Programmers_Guide.pdf

The sound modules are separated parts that make the sound system of the engine:
* [[Sound Manager]] - keeps track of loaded sounds, sound sources, and can initialize OpenAL to allow for playing audio and can ONLY opens default sound device
* [[Sound Source]] - allows for playing a given sound buffer, can change how the audio is played (EX: different pitch or gain)
* [[Sound Buffer]] -  can load given sound file and returns the "ID" which corresponds to the loaded sound (There's ONLY support for .wav formats)
* [[Sound Device]] - initializes the sound device and OpenAL, by default selects the default sound device and otherwise if given specific sound device name

(Show how to use each of the modules)

(Show how to properly close OpenAL)