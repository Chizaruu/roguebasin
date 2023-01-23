# Implementing sound in C and C Plus Plus

## Introduction

Sound, in the meaning of one shot sound effects, background ambiance and background music, is an essential element of modern games. A good sound can build up the atmosphere far better than a thousand words.

The implementation of sound, however, is considered a difficult task, especially for inexperienced programmers. Fortunately, there exist libraries that allow developers to introduce sound to any C and C++ program. One is called FMOD Ex, by Firelight Technologies Pty, Ltd. and another is called [Audiere](http://audiere.sourceforge.net/).

## Audiere

Audiere is an open-source and free interface for C and C++. Installing it is as easy as including the appropriate DLL file and include file in your Makefile or IDE of choice -- no compilation necessary to get a binary, since they are included in the download from the site.

The purpose of Audiere is to provide developers with a braindead easy API, support for all the major file formats (WAVs, FLAC, MP3, and more!), and to provide other basic functionality and capabilities that a programmer may find useful in their sound programming endeavors.

## FMOD

The first thing you need to do is to download the library from [FMOD site](http://www.fmod.org/). The library is available free of charge for anyone, provided you use it only for noncommercial productions. The installation file includes an extensive programmer's API reference, so you can browse it to learn to use FMOD. After that, you just link your project with the appropriate library and you're ready to go.

There is one thing you should be aware of: if you use the C++ interface of FMOD, it won't compile with MinGW. It is a known problem and it can be avoided by either using the C interface or writing your own C++ code that uses FMOD's C interface internally.

Below you can find example code that should make FMOD reproduce looped background music in any C++ project.

## Code: sound.hpp

```cpp
#include "fmod.h" //FMOD Ex

/******** CLASS DEFINITION ********/

class Sound {
private:
    static bool on; //is sound on?
    static bool possible; //is it possible to play sound?
    static char * currentSound; //currently played sound
    //FMOD-specific stuff
    static FMOD_RESULT result;
    static FMOD_SYSTEM * fmodsystem;
    static FMOD_SOUND * sound;
    static FMOD_CHANNEL * channel;

public:
    static void initialise (void); //initialises sound

    //sound control
    static void setVolume (float v); //sets the actual playing sound's volume
    static void load (const char * filename); //loads a soundfile
    static void unload (void); //frees the sound object
    static void play (bool pause = false); //plays a sound (may be started paused; no argument for unpaused)

    //getters
    static bool getSound (void); //checks whether the sound is on

    //setters
    static void setPause (bool pause); //pause or unpause the sound
    static void setSound (bool sound); //set the sound on or off

    //toggles
    static void toggleSound (void); //toggles sound on and off
    static void togglePause (void); //toggle pause on/off
};
```

## Code: sound.cpp

```cpp
#include "sound.hpp"

/******** CLASS VARIABLE DECLARATIONS ********/

bool Sound::on = true; //is sound on?
bool Sound::possible = true; //is it possible to play sound?
char * Sound::currentSound; //currently played sound
//FMOD-specific stuff
FMOD_RESULT Sound::result;
FMOD_SYSTEM * Sound::fmodsystem;
FMOD_SOUND * Sound::sound;
FMOD_CHANNEL * Sound::channel;

/******** METHODS' IMPLEMENTATIONS ********/

//initialises sound
void Sound::initialise (void) {
    //create the sound system. If fails, sound is set to impossible
    result = FMOD_System_Create(&fmodsystem);
    if (result != FMOD_OK) possible = false;
    //if initialise the sound system. If fails, sound is set to impossible
    if (possible) result = FMOD_System_Init(fmodsystem,2, FMOD_INIT_NORMAL, 0);
    if (result != FMOD_OK) possible = false;
    //sets initial sound volume (mute)
    if (possible) FMOD_Channel_SetVolume(channel,0.0f);
}

//sets the actual playing sound's volume
void Sound::setVolume (float v) {
    if (possible && on && v >= 0.0f && v <= 1.0f) {
        FMOD_Channel_SetVolume(channel,v);
    }
}

//loads a soundfile
void Sound::load (const char * filename) {
    currentSound = (char *)filename;
    if (possible && on) {
        result = FMOD_Sound_Release(sound);
        result = FMOD_System_CreateStream(fmodsystem,currentSound, FMOD_SOFTWARE, 0, &sound);
        if (result != FMOD_OK) possible = false;
    }
}

//frees the sound object
void Sound::unload (void) {
    if (possible) {
        result = FMOD_Sound_Release(sound);
    }
}

//plays a sound (no argument to leave pause as dafault)
void Sound::play (bool pause) {
    if (possible && on) {
        result = FMOD_System_PlaySound(fmodsystem,FMOD_CHANNEL_FREE, sound, pause, &channel);
        FMOD_Channel_SetMode(channel,FMOD_LOOP_NORMAL);
    }
}

//toggles sound on and off
void Sound::toggleSound (void) {
    on = !on;
    if (on == true) { load(currentSound); play(); }
    if (on == false) { unload(); }
}

//pause or unpause the sound
void Sound::setPause (bool pause) {
    FMOD_Channel_SetPaused (channel, pause);
}

//turn sound on or off
void Sound::setSound (bool s) {
    on = s;
}

//toggle pause on and off
void Sound::togglePause (void) {
    FMOD_BOOL p;
    FMOD_Channel_GetPaused(channel,&p);
    FMOD_Channel_SetPaused (channel,!p);
}

//tells whether the sound is on or off
bool Sound::getSound (void) {
    return on;
}
```

## Code: Conclusion

With this code, adding a looped background track is as easy as adding these lines to your main.cpp file:

```cpp
Sound::initialise();
Sound::load("yoursound.mp3");
Sound::play();
```

There! You have sound playing in the background while the rest of the program continues!

For details about the used FMOD functions, as well as others, please refer to FMOD API documentation.

by Dominik "Mingos" Marczuk
