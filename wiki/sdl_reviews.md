# SDL:Reviews

---

## Review of the [Simple DirectMedia Layer](sdl.md) by PaulBlay

### Usage background

Preferred IDE : Visual C++ 2008 Express Edition

Preferred Platform : Windows XP

Other requirements sought : Unicode support / Works with SQLite

### Experience with SDL

SDL with SDL_ttf is one of the ports supported by Vanilla [Angband](angband.md). It was pretty easy to install SDL (not so easy for SDL_ttf) and the code to produce an window was quite a bit simpler than wrangling with the Windows API directly. It works well with VC++, the minor 'gotcha's being that you can't use the Debug version of the runtime library and you need to add the parameter SDL_INIT_NOPARACHUTE to the SDL_Init() function or you will not be able to set break points / step through the code. The core SDL itself has large 'gaps' in functionality that are, in theory, covered by separately distributed libraries (such as the afore mentioned SDL_ttf which handles fonts and text display). SDL is distributed with the required libraries and dll files pre-compiled for Windows.

The instructions for SDL_ttf were not as clear and it took me a few times round before I got my project compiling smoothly using it.

So far SDL 'plays nice' with the other bits I'm using, such as SQLite.

### Conclusion

Practical and usable. Lacks the flair and professional looks of some other solutions looked at, but is (so far) better behaved.

### Back to [SDL](sdl.md)
