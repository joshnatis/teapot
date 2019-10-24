# teapot
![Logo](images/teapot-logo-small.png) ![Screenshot](images/teapot-screenshot2.png)

## A word of caution
The tea isn't finished brewing yet! I'm still in the middle of ~~porting this to Linux~~, adding features, learning about licenses, and ~~creating a configuration script~~. This is very much a work in progress. Otherwise, enjoy :)

Also, if you want to watch me explain how the program works (for my own future reference), you can click [here](https://www.youtube.com/watch?v=oJpkSBohS0U).

## What is teapot?
**teapot** is a recursive acronym, short for *teapot, enjoy a peaceful, open tune*. Legend has it that the *o* actually stands for *oraguntan*, but $CREATOR will never tell. Let's also pretend that the *o* stands for *open* in reference to Open Source, and not because I couldn't think of any other words that start with *o*.

Really, **teapot** is a minimal text-based command-line music player -- noteably with no dependencies (well, almost). This is not bloatware.

## How to set up teapot
1. `git clone https://github.com/joshnatis/teapot.git`
1. `cd teapot/`
1. `chmod +x configurate`
1. `./configurate`

You're done!

This is what the script does: 
* checks if your operating system is compatible
* checks if you have the prerequisites installed
* asks you for the directory where most of your music files are contained, so that you can simply call `teapot` and have your songs shuffle automatically.
* makes certain that there is no other executable named `teapot` installed (because one exists)
	* if another teapot is installed, our executable is renamed to `teapot2`
* makes the script executable
* asks you for the location of your other shell scripts and moves `teapot` there (or allows you to move the script manually)
* cleans up the mess by deleting the cloned folder and all of its contents

## How to use teapot
Option 1: shuffle songs from your default directory
<pre> teapot </pre>
Option 2: shuffle songs from a specified directory
<pre> teapot /path/to/directory </pre>
Now go drink your tea, it's that simple.


## Compatability notes
**teapot** has been tested on multiple computers and shells, and linted with [shellcheck](https://www.shellcheck.net/). It was confirmed to work on MacOS, Manjaro Linux, and noteably Arch Linux. It should work in most shells, but when in doubt, use `bash`. If you find any bugs or have any issues, please reach out to me at:

`josh at josh8 dot com`

## Dependencies (and lack thereof)
It was my goal to have **teapot** be free of any dependencies. If you needed ten external libraries installed just to use my program, you'd might as well go ahead and download a full fledged application (maybe even with an ooey gooey GUI... fooey).

On MacOS, **teapot** uses the `afplay`and `afinfo` commands to deal with audio -- these are native to the OS and come installed by default. On Ganoo Linocks (read: any Linux distribution), **teapot** uses `ffmpeg` for the same purposes. This is far from ideal, but, to be fair, if you're a Linux user you probably have `ffmpeg` installed already. The script can also work with `mpg123` (in fact there is a commented line containing the `mpg123` command that can replace `ffmpeg`), or any utility that can play audio (I would have used `aplay` but it only works for raw audio files).
