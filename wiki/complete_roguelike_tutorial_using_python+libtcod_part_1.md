# Complete Roguelike Tutorial, using python+libtcod, part 1

---

This is part of a series of tutorials; the main page can be found [here](complete_roguelike_tutorial_using_python+libtcod.md).

The tutorial uses libtcod version 1.6.0 and above.

If you are looking for a Python 3 version, see here

## Graphics

---

### Setting it up

---

Ok, now that we got that out of our way let's get our hands dirty!

#### Windows

##### Python

If you haven't already done so, [download and install Python 2.7](http://www.python.org/download/). Any version of Python 2, from 2.6 and above should be fine, but the latest update (of Python 2, not Python 3) is of course always preferable.

There are reports that for older versions of libtcod on 64-bit Windows (Windows 7) that the 32 bit version is preferable, since the 64 bit version of Python seems to cause problems with libtcod. This was been tested on Windows 10 with libtcod 1.6, and was not reproducible, so it's up to you.

It might be advisable to go with 32 bit just to keep things simple, and when you're gone through the tutorial look at switching to 64 bit if it matters to you to do so, and if you do encounter errors specifically related to this, report them on the [libtcod bug tracker](https://bitbucket.org/libtcod/libtcod/issues/).

##### libtcod

Download the latest release of [libtcod 1.6](https://bitbucket.org/libtcod/libtcod/downloads) and extract it somewhere. Be warned that both Python and libtcod must either be **both 32 bit**, or **both 64 bit**. If you get dll loading errors, getting this wrong is the most likely cause. libtcod will now (as of 1.6.2 and above) check they are compatible, and tell you if they are not.

###### Check if libtcod works

libtcod is compiled with the latest official release of Visual Studio. In an ideal world, it should just run, and if you have recently updated games installed from Steam or perhaps even some modern applications, it should. However, some people lack the support for that latest release of Visual Studio, and these steps help update your computer to allow libtcod to be run.

1. Within the libtcod release you just extracted, you will see a file called samples.exe.
2. Run samples.exe and verify that it appears to work. If it opens a colourful window, then it is now verified as working for you.
3. If it just doesn't start and instead you see a message about _vcruntime140.dll_ being required, then you need to install the correct Visual Studio 2015 runtime.
4. Go to the latest Microsoft Visual Studio 2015 [runtime download page](https://www.microsoft.com/en-us/download/details.aspx?id=53587), and download and install the runtime that matches the Python and libtcod you are using. If you are using 32 bit Python and libtcod, this will be the x86 runtime. If you are using the 64 bit Python and libtcod, this will be the x64 runtime. (Tip: If you think you might switch from 32 bit to 64 bit in the future, hedge your bets and install both runtimes to prevent future problems.)
5. Once you've installed the correct runtime, run samples.exe and verify that it now appears to work. If it still gives the same message, you didn't install the correct runtime. If it opens a colourful window, then it is now verified as working for you.
6. If it is not verified as working for you, you can open a libtcod bug report [on BitBucket](https://bitbucket.org/libtcod/libtcod/issues?status=new&status=open).

You've verified that libtcod is working? Great! You can move onto the next section.

##### Your project folder

Now create your project's folder. Inside it, create an empty file with a name of your choice. We're using firstrl.py. It'll make the tutorial easier to just use the same name, and you can always rename it later.

![project-folder-empty](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/part_1/Windows-01-162-project-folder-empty.png)

From within the libtcod build you downloaded above, select and copy the relevant DLLs.

![dlls](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/part_1/Windows-02-162-dlls.png)

From within the libtcod build you downloaded above, select and copy the _libtcodpy_ folder.

![libtcodpy-folder](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/part_1/Windows-03-162-libtcodpy-folder.png)

From within the libtcod build you downloaded above, select and copy the _arial10x10.png_ font. If you choose a different one, you'll need to adjust how you do the tutorial accordingly. It's simpler to just use the same one as we do, and change it later, if it suits you to do so.

![font](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/part_1/Windows-04-162-font.png)

At this point, you should have a folder that looks like this:

![project-folder-ready](./assets/images/tutorials/complete_roguelike_tutorial_using_python+libtcod/part_1/Windows-05-162-project-folder-ready.png)

You're ready to start editing _firstrl.py_!

#### Autotools platforms (Linux, MacOS, ...)

##### Python

The tutorial is written for Python 2, so ensure you have it installed and know what it is called.

##### SDL2

If your OS or OS distribution has a package that can be used for SDL2 development, then the best option is to install it. This will ensure that you can easily update it.

In the event that you cannot find a package, you can compile it yourself as a last resort. To do so, download the supported SDL2 revision, build and install it:

```bash
$ curl -o sdl.tar.gz http://hg.libsdl.org/SDL/archive/007dfe83abf8.tar.gz
$ tar -xf sdl.tar.gz
$ cd SDL-007dfe83abf8/
$ mkdir -p build
$ cd build
$ ../configure
$ make
$ sudo make install
```

This will place the libraries at `/usr/local/lib/` and the development headers at `/usr/local/include/SDL2/`.

##### libtcod

Download the latest libtcod release and compile it:

```bash
$ wget https://bitbucket.org/libtcod/libtcod/downloads/20161228-libtcod-1.6.2.tbz2
$ tar xf 20161228-libtcod-1.6.2.tbz2
$ cd 20161228-libtcod-1.6.2
$ cd build/autotools/
$ autoreconf -i
$ ./configure
$ make
$ cd ../..
```

Now you should be in the root libtcod directory. You can see the compiled library like:

```bash
$ find . -name "libtcod.so"
./build/autotools/.libs/libtcod.so
```

However, notice this .so is actually a link to this other .so.0.0.0 file in the same directory:

```bash
$ readlink ./build/autotools/.libs/libtcod.so
libtcod.so.0.0.0
```

##### Your project folder

Now you're ready to make a directory for your new project, copy the compiled library and links, plus the python library and a font into it:

```bash
$ mkdir -p ~/tutorial
$ cp -dr build/autotools/.libs/libtcod.so* python/libtcodpy data/fonts/arial10x10.png ~/tutorial/
$ cd ~/tutorial
```

You should be able to see something similar to this:

```bash
$ ls -lgo
total 1388
-rw-rw-r-- 1   14870 Dec 31 15:57 arial10x10.png
drwxrwxr-x 2    4096 Dec 31 15:57 libtcodpy
lrwxrwxrwx 1      16 Dec 31 15:57 libtcod.so -> libtcod.so.0.0.0
lrwxrwxrwx 1      16 Dec 31 15:57 libtcod.so.0 -> libtcod.so.0.0.0
-rwxrwxr-x 1 1413856 Dec 31 15:57 libtcod.so.0.0.0
```

Now you're ready to start.

## Choice of code editor

---

If you're just starting out with Python, you'll find that many Python coders just use a simple editor and run their scripts from a console to see any debugging output. Most Python coders don't feel the need to use a fancy IDE! On Windows, Notepad++ is an excellent bet; most Linux programmers already have an editor of choice. Almost all editors allow you to configure shortcut keys (like F5 for instance) to quickly run the script you're editing, without having to switch to a console.

[See this page](complete_roguelike_tutorial_using_python+libtcod_extras.md#a-neat-python-shortcut-for-notepad) if you want to set up a Notepad++ shortcut with a couple of nice features for debugging, or if you tried to roll your own and hit the infamous "module not found" error.

Another quick note, if you're using IDLE. It doesn't seem to let libtcod clean up properly on exit, so it crashes when you end a script. This seems to be more of an IDLE problem than a libtcod problem so there's no fix in sight, for now. Using any of the setups recommended above will work just fine.

## Showing the @ on screen

---

This first part will be a bit of a crash-course. The reason is that you need a few lines of boilerplate code that will initialize and handle the basics of a libtcod window. And though there are many options, we won't explain them all or this part will really start to drag out. Fortunately the code involved is not as much as in many other libraries!

First we import the library. The name _libtcodpy_ is a bit funky (sorry Jice!) so we'll rename it to just libtcod.

```python
import libtcodpy as libtcod
```

Then, a couple of important values. It's good practice to define special numbers that might get reused. Many people capitalize them to distinguish from variables that may change.

```python
SCREEN_WIDTH = 80
SCREEN_HEIGHT = 50
LIMIT_FPS = 20
```

Now, something libtcod-specific: we're going to use a custom font! It's pretty easy. libtcod comes bundled with a few fonts that are usable right out of the box. Remember however that they can be in different formats, and you'll need to tell it about this. This one is "grayscale" and using the "tcod layout", most fonts are in this format and thus end with \_gs_tc. If you wanna use a font with a different layout or make your own, the [docs on the subject](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/console_set_custom_font.html?c=false&cpp=false&cs=false&py=true&lua=false) are really informative. You can worry about that at a later time though. Notice that the size of a font is automatically detected.

```python
libtcod.console_set_custom_font('arial10x10.png', libtcod.FONT_TYPE_GREYSCALE | libtcod.FONT_LAYOUT_TCOD)
```

This is probably the most important call, initializing the window. We're specifying its size, the title (change it now if you want to), and the last parameter tells it if it should be fullscreen or not.

```python
libtcod.console_init_root(SCREEN_WIDTH, SCREEN_HEIGHT, 'python/libtcod tutorial', False)
```

For a real-time roguelike, you wanna limit the speed of the game (frames-per-second or FPS). If you want it to be turn-based, ignore this line. (This line will simply have no effect if your game is turn-based.)

```python
libtcod.sys_set_fps(LIMIT_FPS)
```

Now the main loop. It will keep running the logic of your game as long as the window is not closed.

```python
while not libtcod.console_is_window_closed():
```

For each iteration we'll want to print something useful to the window. If your game is turn-based each iteration is a turn; if it's real-time, each one is a frame. Here we're setting the text color to be white. [There's a good list of colors you can use here](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/color.html?c=false&cpp=false&cs=false&py=true&lua=false), along with some info about mixing them and all that. The zero is the console we're printing to, in this case the screen; more on that later.

```python
    libtcod.console_set_default_foreground(0, libtcod.white)
```

Don't forget the indentation at the beginning of the line, it's extra-important in Python. **Make sure you don't mix tabs with spaces for indentation!** This comes up often if you copy-and-paste code from the net, and you'll see an error telling you something about the indentation (that's a pretty big clue right there!). Choose one option and stick with it. In this tutorial we're using the 4-spaces convention, but tabs are easy to work with in many editors so they're a valid choice too.

Now print a character to the coordinates (1,1). Once more the first zero specifies the console, which is the screen in this case. Can you guess what that character is? No, it doesn't move yet!

```python
    libtcod.console_put_char(0, 1, 1, '@', libtcod.BKGND_NONE)
```

At the end of the main loop you'll always need to present the changes to the screen. This is called flushing the console and is done with the following line.

```python
    libtcod.console_flush()
```

Ta-da! You're done. Run that code and give yourself a pat on the back!

> **Common reasons the code won't run.**
>
> Python errors? Using Python 3?
>
> We said above that this tutorial is only for Python 2. So use Python 2, with Python 3 you are on your own. They're different languages, it won't just magically work!
>
> On Windows? Getting the "Incompatible architecture" error?
> Make sure your Python and libtcod are either BOTH 32 bit, or BOTH 64 bit.
>
> On Windows? Getting the "The specified module could not be found" error?
>
> You didn't verify that libtcod was working. Go back and do it
>
> Still blocked for other reasons? Check out the problems page.

Note that since we don't have any input handling code, the game may crash on exit (it won't process the OS's requests to close). Oops! Don't worry though, this problem will go away as soon as we add keyboard support.

[Here](complete_roguelike_tutorial_using_python+libtcod_part_1_code.md#showing-the--on-screen)'s the complete code so far.

## Moving around

---

That was pretty neat, huh? Now we're going to move around that @ with the keys!

First, we need to keep track of the player's position. We'll use these variables for that, and take the opportunity to initialize them to the center of the screen instead of the top-left corner. This can go just before the main loop.

```python
playerx = SCREEN_WIDTH/2
playery = SCREEN_HEIGHT/2
```

There are functions to check for pressed keys. When that happens, just change the coordinates accordingly. Then, print the @ at those coordinates. We'll make a separate function to handle the keys.

```python
def handle_keys():
    global playerx, playery

    #movement keys
    if libtcod.console_is_key_pressed(libtcod.KEY_UP):
        playery -= 1

    elif libtcod.console_is_key_pressed(libtcod.KEY_DOWN):
        playery += 1

    elif libtcod.console_is_key_pressed(libtcod.KEY_LEFT):
        playerx -= 1

    elif libtcod.console_is_key_pressed(libtcod.KEY_RIGHT):
        playerx += 1
```

Done! These are the arrow keys, if you want to use other keys here's a [reference](http://roguecentral.org/doryen/data/libtcod/doc/1.5.1/html2/console_keycode_t.html?c=false&cpp=false&cs=false&py=true&lua=false) (pay attention to the Python-specific notes).

While we're at it, why not include keys to toggle fullscreen mode, and exit the game? You can put this at the beginning of the function.

```python

    key = libtcod.console_check_for_keypress()
    if key.vk == libtcod.KEY_ENTER and key.lalt:
        #Alt+Enter: toggle fullscreen
        libtcod.console_set_fullscreen(not libtcod.console_is_fullscreen())

    elif key.vk == libtcod.KEY_ESCAPE:
        return True  #exit game
```

From now on, we'll show code for a **real-time game** with a green background, and code for a **turn-based game** with a blue background.

Notice a subtle difference here. The _console_is_key_pressed_ function is useful for real-time games, since it checks if a key is pressed with no delays. _console_check_for_keypress_, on the other hand, treats the key like it's being typed. So after the first press, it will stop working for a fraction of a second. This is the same behavior you see when you type, otherwise pressing a key would result in you typing 3 or 4 letters! It's useful for all commands except movement, which you usually want to react as soon as possible with no delays, and continue for as long as you press the movement keys.

Now here's an important thing: you can use that first line to distinguish between real-time and turn-based gameplay! See, _console_check_for_keypress_ won't block the game. But if you replace it with this line:

```python
    key = libtcod.console_wait_for_keypress(True)
```

Then the game won't go on unless the player presses a key. So effectively you have a turn-based game now.

Now, the main loop needs to call this function in order for it to work. If the returned value is True, then we "break" from the main loop, ending the game. The inside of the main loop should now look like this:

```python
    libtcod.console_set_default_foreground(0, libtcod.white)
    libtcod.console_put_char(0, playerx, playery, '@', libtcod.BKGND_NONE)

    libtcod.console_flush()

    #handle keys and exit game if needed
    exit = handle_keys()
    if exit:
        break
```

The reason why we draw stuff before handling key input is that, in a turn-based game, the first screen is shown before the first key is pressed (otherwise the first screen would be blank).

One more thing! If you try that, you'll see that moving you leave around a trail of little @'s. That's not what we want! We need to clear the character at the last position before moving to the new one, this can be done by simply printing a space there. Put this just before _exit = handle_keys()_.

```python
    libtcod.console_put_char(0, playerx, playery, ' ', libtcod.BKGND_NONE)
```

[Here](complete_roguelike_tutorial_using_python+libtcod_part_1_code.md)'s a rundown of the whole code so far.

[Go on to the next part](complete_roguelike_tutorial_using_python+libtcod_part_2.md).
