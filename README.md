# teapot
![Logo](images/teapot-logo-small.png) ![Screenshot](images/teapot-screenshot2.png)

## A word of caution
The tea isn't finished brewing yet! I'm still in the middle of ~~porting this to Linux~~, adding features, ~~learning about licenses~~, and ~~creating a configuration script~~. This is very much a work in progress. Otherwise, enjoy :)

Also, if you want to watch me explain how the program works (for my own future reference), you can click [here](https://www.youtube.com/watch?v=oJpkSBohS0U).

## What is teapot?
**teapot** is a recursive acronym, short for *teapot, enjoy a peaceful, open tune*. Legend has it that the *o* actually stands for *oraguntan*, but $CREATOR will never tell. Let's also pretend that the *o* stands for *open* in reference to Open Source, and not because I couldn't think of any other words that start with *o*.

Really, **teapot** is a minimal text-based command-line music player -- noteably with no dependencies (well, almost). This is not bloatware.

## How to use teapot
Option 1: shuffle songs from your default directory
<pre> teapot </pre>
Option 2: shuffle songs from a specified directory
<pre> teapot /path/to/directory </pre>
Now go drink your tea, it's that simple.

## How to set up teapot
1. `git clone https://github.com/joshnatis/teapot.git`
1. `cd teapot/`
1. `chmod +x configurate`
1. `./configurate`
1. Move `teapot` to your desired directory and delete any extraneous files.

You're done!

### So you don't want to use the configuration script...
That's okay. Here's what you should do:

1. Download `teapot` (unless you're on Windows :P)

2. Open up the source code in your favorite editor and scroll all the way down to the bottom of the file. You'll see this line `MUSIC_DIR=~/Downloads #default directory of audio files` -- change the path to your desired directory (one which contains your music files).

3. If you're on a Mac, search the script for `ffmpeg`, `ffplay`, and `ffprobe`, comment those lines out (using the `#`), and uncomment the lines next to them containing `afplay` and `afinfo`, respectively. Others, make sure you have `ffmpeg` installed (you can check this with `which ffmpeg` or `ffmpeg -h`).

4. Make the script executable with `chmod +x teapot`.

5. Delete any junk that's left over (i.e. remove the directory if you cloned the git repo). That's all, I hope. Don't forget to enjoy!

## Compatability notes
**teapot** has been tested on multiple computers and shells, and linted with [shellcheck](https://www.shellcheck.net/). It was confirmed to work on MacOS, Manjaro Linux, and Arch Linux. It should work in most shells, but when in doubt, use `bash`. If you find any bugs or have any issues, please reach out to me at:

`josh at josh8 dot com`

## Dependencies (and lack thereof)
It was my goal to have **teapot** be free of any dependencies. If you needed ten external libraries installed just to use my program, you'd might as well go ahead and download a full fledged application (maybe even with an ooey gooey GUI... fooey).

On MacOS, **teapot** uses the `afplay`and `afinfo` commands to deal with audio -- these are native to the OS and come installed by default. On Ganoo Linocks (read: any Linux distribution), **teapot** uses `ffmpeg` for the same purposes. This is far from ideal, but, to be fair, if you're a Linux user you probably have `ffmpeg` installed already. The script can work with any utility that can play audio, though.

## Licensing
This ware is presented to you with no warranty, but with best wishes. The code is MIT Licensed. Do stuff with it.

I put forward my preference that you "pass it forward" by keeping the ware free and open, if you choose to redistribute it. 
