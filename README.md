# teapot
![Logo](teapot-logo-small.png) ![Screenshot](teapot-screenshot.png)

## What is teapot?
**teapot** is a recursive acronym, short for *teapot, enjoy a peaceful, open tune*. Legend has it that the *o* actually stands for *oraguntan*, but $CREATOR will never tell. Let's also pretend that the *o* stands for *open* in reference to Open Source, and not because I couldn't think of any other words that start with *o*.

Really, **teapot** is a minimal text-based terminal music player -- noteably with no dependencies (at least I think so :P). As such, **teapot**'s only functionality is to shuffle your music.

## How to set up teapot
Firstly, make sure you choose the correct version to download. On MacOS, the default terminal audio player command is *afplay*, while on Linux it's *mpg123* -- to keep you from manually substituting one command for the other depending on your system I went ahead and made two different versions.

Next, move **teapot** to the directory where you keep all of your shell scripts. If you don't have one already, I suggest you see my quick guide on setting up [here](https://github.com/joshnatis/shell-skriptz). Now, make the script executable with the command *chmod +x teapot*. At this point, if you'd like, you can rename the script to something else (such as 'ipod'), or make an alias for it. 

Lastly, open the script in your favorite editor and change the variable MUSIC_DIR to have the value of the default directory where you keep your audio files. This way, you can simply type **teapot** and have your songs shuffle automatically. 

You're done!

## How to use teapot
Option 1: shuffle songs from your default directory (stored in MUSIC_DIR)
<pre> teapot </pre>
Option 2: shuffle songs from a specified directory
<pre> teapot /path/to/directory </pre>
Now go drink your tea, it's that simple.
