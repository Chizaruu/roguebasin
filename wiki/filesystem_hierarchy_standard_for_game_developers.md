# Filesystem hierarchy standard for game developers

---

[Original](http://roguebasin.roguelikedevelopment.org/index.php?oldid=47572) by Ray Dillinger

Based on FHS 2.3, which is online at <http://www.pathname.com/fhs/> .

This is strictly about the Unix filesystem hierarchy standard, which is (all or mostly) shared by Linux and BSD. Other systems may have different standards.

## General

---

There are three installation scenarios we care about in installing games: user-specific, local, and standard.

User-specific installation is installation of a game by someone who doesn't have root privileges on the machine. This puts all files in that user's home directory and owned by that user. If the user's account (and home directory) is deleted, the game is uninstalled. The FHS imposes no internal structure on a user-specific installation; a game developer may structure it however he or she likes. If you support user-specific installation, then your installer should do it if it detects that it is unable to get the privileges to write in the directories it would use for a standard installation. User-specific installations are often favored by developers who want a personal copy of the game and its files to scribble on.

Local installation is installation of a game by hand, by the local site administrator. Generally it is used when a standard installation has been done but the site administrator wants the local machine (or the local network) to run a version of the game different from the standard installation. This can be because the game had to be changed to fit the local OS conventions or local censorship policy, or because they don't want a tournament to be interrupted by an automated package update, or because they are actively modifying the game and don't want their experimentation to affect the standard installation, etc. A local installation installs its files where the system will find them before it finds the files of the standard installation.

A legal game installer never does a local installation by default: 'local' directories are reserved specifically for the use of the site administrators. You may include an automated way to make a local installation, but it must never be called other than by the explicit and intentional command of a local site administrator. In particular, ordinary package installations, upgrades, downgrades, or removals must not affect a local installation.

A standard installation is a game installed in a standard way - typically by root in the course of updating or installing a standard package, without any special effort.

## Example

---

A game named 'gargoyle' will be used as an example. Whenever you see that word in one of the directory or filenames mentioned below, make an appropriate substitution for your game.

Where the FHS specifies a directory used only by a single game, it doesn't specify the layout or filenames within the directory. Thus, whenever this document talks about a set of files being in a particular directory, you're allowed to name them anything and lay them out however you like within that directory.

### Game binary

- Is the file /usr/games/gargoyle in a standard installation.
- Is the file /usr/local/games/gargoyle in a local installation.

### Game man page

- Is the file /usr/share/man/man6/gargoyle.6 in a standard installation. (note, the filename may be gargoyle.6.gz or something rather than gargoyle.6 depending on man configuration and page storage format)
- Is the file /usr/local/man/man6/gargoyle.6 in a local installation. (note, the filename may be gargoyle.6.gz rather than gargoyle.6, as above)

Note: /usr/local/share/man/ is the same directory as /usr/local/man (one or the other is a symlink - which one may vary from system to system).

### Architecture-independent static game data

This is data that does not change as a result of playing the game. It specifically does not include high score tables, savefiles, logs, etc. If your game reads script files that determine game behavior or probabilities, etc, when initializing, those script files are included. If your game displays help text that's stored in files, the files belong here.

- Go in a directory named /usr/share/games/gargoyle in a standard installation.
- Go in a directory named /usr/local/share/games/gargoyle in a local installation.

Note: Under ordinary circumstances, attempts to write these locations usually fail. The game or user running it is not likely to be allowed to write in these directories, and even if they are, the partition on which they reside is likely to be mounted readonly in server locations.

Note that /usr/share (and /usr/local/share) and its subdirectories including the static game data and man pages may be on a drive mounted by several different machines of different architectures which all run the same version of the OS and "share" the installation data. If you have data that makes sense on an i386 machine and doesn't make sense on a SPARC machine, don't put it here.

### Source code

Note that most standard game installers do not include source code - usually it is packaged separately in another installer, and sometimes it is not provided by the developer.

- Goes in a directory named /usr/src/gargoyle in a standard installation.
- Goes in a directory named /usr/local/src/gargoyle in a local installation.

### Modifiable files

Including high score tables, game play logs, savefiles, etc ...

- Go in a directory named /var/games/gargoyle in a standard installation.
- Go in a directory named /var/local/games/gargoyle in a local installation.

### Configuration files

Site-wide "default" configuration file, if singular, is the file /etc/gargoyle in both standard and local installations.

Site-wide "default" configuration files, if there is more than one file, all go in a directory named /etc/gargoyle in both standard and local installations.

A per-player config file, if there's only one per player, is the file .gargoyle in the user's home directory.

If there is more than one per-player config file, then all of them go into a directory named '.gargoyle' in each player's home directory.

Note that per-player configuration files should not be created by the installer. They should only be created when the player, having actually invoked the game, makes a configuration change that differs from the site-wide or default configuration. If non-players, or players whose configuration is identical to the default or site-wide configuration, have their own configuration files, it wastes disk space.

Note that users' home directories are usually in /home/$username, but the setup is site-specific, and some users (usually root at least) don't use /home anyway. So don't rely on the directory named /home. Use getenv("HOME") or getpwuid(geteuid())->pw_dir or whatever equivalent you have on your system or in the libraries of your preferred language to find the user's home directory. 999 times out of a thousand it'll be in /home, but you never know.
