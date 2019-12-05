#!/bin/bash

#==== FIXES ====
#TODO - fix bug with delete not working when asking for index

#==== FEATURES ====
#TODO - add (r)ename option (enter new name: mv $MUSIC_DIR/MUSIC_LIST(SONG_INDEX $MUSIC_DIR/new name)
#TODO - get_duration function (possibly show duration on main screen, update it in loop - SECONDS)
#TODO - consider putting caffeinate in setup 
#TODO - Use recursive search and find all music files on computer -> music_list
	# Should be an option not default behavior, probably intensive
#TODO - add minigames to mp3 player
	#(1) "rate songs", at the end it displays your favorite song and the ratings you gave
	#(2) somehow find tempo of song and display animation correspondingly (using $SECONDS)
	#(3) random animation
#TODO - show metadata option (afinfo)
#TODO - integrate all options from mpg123/ffplay into linux version
#TODO - arrowkeys for next/back
#TODO - mute
#TODO - queue option


# ========== GLOBAL STATE VARIABLES ==========
#MUSIC_DIR - Directory with music files (either default or specified with $1)
#NUM_SONGS - number of songs in $MUSIC_DIR
#MUSIC_LIST - array of songs in $MUSIC_DIR
#REQUESTED_INDEX - either SHUFFLE or number between 0 - NUM_SONGS, used as parameter for play_song (processed into SONG_INDEX)
#SONG_INDEX - random or specified number between 0 - NUM_SONGS, used as index to access song file from MUSIC_LIST
#EXTENSIONS - file extensions *.mp3, *.wav, etc (feel free to modify, preserving current format)
#HISTORY - array of song indeces which have been played in current session
#QUEUE - array of songs requested to be played next
# ========== /GLOBAL VARIABLES ==========


# ========== HELPER FUNCTIONS ==========

#theme function
make_colorful() {
	#tput setab 4
	#tput setaf 7
	:
}

clearscreen() {
	clear && printf '\e[3J'
}

ctrl_c() {
	stop_song
	tput sgr0 	#unbold
	stty sane
	clearscreen 
	exit 0
}

pkg_installed() {
	man "$1" &> /dev/null
	local man_status=$? #if the man page exists status will be 0, else 1

	installed=$(which "$1")

	if [ $man_status -eq 0 ] || [ -n "$installed" ]; then
		return 0
	else
		return 1
	fi
}

is_not_base_10() {
	local first_char="${1:0:1}"
	local var="$1"
	local num_digits=${#var}

	#if input has a prefix of 0 but continues, it is signifying a number in another base system
	if [ "$first_char" = 0 ] && [ "$num_digits" != 1 ]; then
		return 0 #successfully not base 10
	else
		return 1
	fi
}

is_not_digit() {

	local re='^[0-9]+$'

	if ! [[ "$1" =~ $re ]] || is_not_base_10 "$1"; then # $1 is not a digit, return error code 0 (sucess)
   		return 0
   	fi

   	return 1 # $1 is a digit, return error code 1 (failure)
}

bold() {
	tput bold
}

unbold() {
	tput sgr0
	make_colorful
}

stop_song() {
	#killall mpg123 &> /dev/null
	#killall ffplay &> /dev/null
	ps -ef | grep ffplay | grep -v grep | awk '{print $2}' | xargs kill

	clearscreen
}

#Finds number of songs in directory (MUSIC_DIR)
num_songs() {
	ls -l "$MUSIC_DIR"/$EXTENSIONS 2> /dev/null | wc -l
	#Note: the command "2> /dev/null" redirects error messages, but leaves regular output
}

is_invalid_index() {
	if is_not_digit "$1" || [ "$1" -gt "$NUM_SONGS" ] || [ "$1" -lt 0 ]; then
		return 0
	fi		
	return 1
}

not_replay() {
	#if this is the first song, or if the current song is already recorded in history
	if [ "${#HISTORY[@]}" -eq 0 ] || [ "$SONG_INDEX" != "${HISTORY[${#HISTORY[@]}-1]}" ]; then
		return 0
	fi
	return 1
}
# ========== /HELPER FUNCTIONS ==========

# ========== MAIN FUNCTIONS ==========
setup() {
	trap ctrl_c INT #if the user enters ctrl -c, allow me to do cleanup first

	#If the number of args > 1, exit
	if [ "$#" -gt 1 ]; then
		bold; echo "Check your arguments! Only provide an [optional] path to your music directory."; unbold
		exit 1 
	#If the number of args = 1, that arg must be a path to your music files
	elif [ "$#" -eq 1 ]; then
		#Set MUSIC_DIR to user-specified path to audio files
		MUSIC_DIR="$1"
	fi

	#cd throws error and exits if improper path
	cd "$MUSIC_DIR" || exit

	#Get the number of songs in the specified directory, make sure it isn't 0
	NUM_SONGS=$(num_songs)
	if [ "$NUM_SONGS" -eq 0 ]; then
		bold; echo "Found no songs in specified directory!"; unbold
		exit 1
	fi

	#----
	#THE FOLLOWING ARE SOLUTIONS TO GLOBS NOT EXPANDING
	#PREVIOUS: EXTENSIONS="*.mp3 *.pcm *.wav *.aac *.ogg *.m4a *.aif *.flac"

	#SOLUTION 1
	EXTENSIONS=()
	ext_list=("*.mp3" "*.pcm" "*.wav" "*.aac" "*.ogg" "*.m4a" "*.aif" "*.flac")
	for ext in "${ext_list[@]}"
	do
		if ls ./$ext &> /dev/null; then #files exist
   			EXTENSIONS="$EXTENSIONS $ext"
   		fi
	done

	#SOLUTION 2 (bash only)
	#shopt -s nullglob

	#----

	#Create array of all audio files in MUSIC_DIR
	MUSIC_LIST=( $EXTENSIONS )
	
	#Will store all songs which have been played in current session
	HISTORY=()

	#Will store songs requested to played next
	QUEUE=()

	make_colorful
	clearscreen
}

#List files/indexes for user and allow them to scroll through in less
show_songs() {
	#NF - Last field (File name), NR - Row number, less -S turns off word fold
	bold; find "$MUSIC_DIR"/${ext_list[@]} 2> /dev/null | awk -F'/' '{print "["NR-1"]\t" $NF}' | less -S; unbold
}


#Set REQUESTED_INDEX to the previous song played
queue_previous() {
	#pop and return ast element of the history array
	REQUESTED_INDEX="${HISTORY[${#HISTORY[@]}-1]}"
}

#Download song, update number of songs, update array of songs
download_song() {
	if pkg_installed "youtube-dl" && pkg_installed "ffmpeg"; then
		bold; read -r -p "Enter URL: " URL && cd "$MUSIC_DIR"; unbold
		youtube-dl --extract-audio --audio-format mp3 --output "%(title)s.%(ext)s" "$URL" 2> /dev/null && NUM_SONGS=$(num_songs) && MUSIC_LIST=( $EXTENSIONS )
		
		local status=$? #if song downloaded successfully, status is 0
		if [ $status = 0 ]; then
			bold; echo "Success! Check your $MUSIC_DIR folder for the file."; unbold; read -t 3
			clearscreen
		else
			tput setaf 1; printf "ERROR"; tput sgr0; make_colorful
			echo ": $URL is not a valid URL."
			read -t 5
			clearscreen
		fi

	else
		bold; echo "You must have youtube-dl and ffmpeg installed to use this feature. Install using your desired package manager then try again."; unbold; read -t 4
	fi
}

display_help_menu() {
	local choice="timeout"
	if [ "$1" = "full" ]; then
		echo "------------------"
		echo " TEAPOT HELP MENU"
		echo "------------------"
		bold; printf "(n)ext"; unbold; echo " - next song"
		bold; printf "(b)ack"; unbold; echo " - previous song"
		bold; printf "(q)uit"; unbold; echo " - exit the music player" 
		bold; printf "(s)top"; unbold; echo " - pause song"
		bold; printf "(r)epeat"; unbold; echo " - loop the current song"
		echo "-----"
		bold; printf "(p)ick"; unbold; echo " - pick a song to play from your library"
		bold; printf "(l)ibrary"; unbold; echo " - display the songs in your library"
		echo "-----"
		bold; printf "(d)ownload "; unbold; echo " - download a song"
		bold; printf "(v)iew commands"; unbold; echo " - print out condensed commands"
		bold; printf "(h)elp"; unbold; echo " - display this help menu"
		echo "-----"
		bold; printf "(1)history"; unbold; echo " - display songs played in the current session"
		bold; printf "(2)queue"; unbold; echo " - add a song to your song queue (play next)"
		bold; printf "(3)view queue"; unbold; echo " - view the items in your queue"
		bold; printf "(4)restart"; unbold; echo " - restart the current song"

		echo "------------------"
		echo ""
		echo "(q)uit help menu";
		#if the song ends, we'll enter the choice="timeout" procedure
		read -n 1 -s -t $(($INT_DURATION - $SECONDS)) choice
		if [ "$choice" = "q" ] || [ "$choice" = "timeout" ]; then
			show_music_player "$(($INT_DURATION - $SECONDS))" "1" #don't reset elapsed time
		else
			choice="timeout"
		fi
	elif [ "$1" = "short" ]; then
		print_ascii_art
		bold; echo "(n)ext, (b)ack, (q)uit, (s)top, (r)epeat, (p)ick, (l)ibrary, (d)ownload, (h)elp"
		echo "(v)iew commands, (1)history, (2)queue, (3)view queue, (4)restart"; unbold
		read -n 1 -s -t $(($INT_DURATION - $SECONDS)) choice
		show_music_player "$(($INT_DURATION - $SECONDS))" "1" #don't reset elapsed time
	fi
}

show_pick_song_options() {
	print_ascii_art
	bold; echo "(q)uit, (l)ibrary"; unbold
	bold; printf "|"; unbold; printf "| $1 Song #: "
}

#Sets REQUESTED_INDEX to user specified song index
pick_song() {
	local index #used to store potential index for next song
	stty sane #fixes strange bug with delete key not being picked up
	show_pick_song_options "$1"

	while :; do
		#read -n 1 makes keys get read right away
		local char
		read -r -n 1 char

		#quit
		if [[ "$char" = "q" ]]; then
			show_music_player && return
		#show songs
		elif [[ "$char" = "l" ]]; then
			show_songs
			index=""
			show_pick_song_options "$1"
		#User pressed enter, assess their index
		elif [[ "$char" = "" ]]; then
			if is_invalid_index "$index"; then
				bold; echo "Index is invalid."; unbold;
				index="" #reset index
				read -t 1
				show_pick_song_options "$1"
				continue #try again
			#Index is valid, submit it
			else
				if [ "$1" = "Play" ]; then
					REQUESTED_INDEX="$index"
				elif [ "$1" = "Queue" ]; then
					QUEUE=("$index" "${QUEUE[@]}")
				fi
				return
			fi
		#User is still typing the index
	   	else
	   		index="$index$char" # append input
	   	fi
	done
}

toggle_repeat_mode() {
	clearscreen
	print_ascii_art
	bold; echo "(q)uit repeat mode"; unbold

	local choice="timeout"

	while :; do
		REQUESTED_INDEX="$SONG_INDEX" #current playing song's index

		read -n 1 -s -t $(($INT_DURATION - $SECONDS)) choice #if the song ends, we'll enter the choice="timeout" procedure
		if [ "$choice" = "q" ]; then
			REQUESTED_INDEX="SHUFFLE" #repeat mode off, go back to shuffle
			return
		elif [ "$choice" = "timeout" ]; then
			SECONDS=0 #reset seconds count before playing next song
			clearscreen
			play_song "$REQUESTED_INDEX"
			bold; echo "(q)uit, (d)isable repeat mode"; unbold
		else
			choice="timeout"
		fi
	done
}

show_music_player() {
	print_ascii_art
	if [ $# -eq 1 ]; then
		show_options "$(($INT_DURATION - $SECONDS))" "1"
	else
		show_options "$INT_DURATION" "1"
	fi
}

print_ascii_art() {
	clearscreen
	#formatting (optimized for 80 by 24 terminal window)
	tput rmam #disables line wrap
	echo " _______________________________"
	echo "| \___===____________________()_\ "
	echo "| |                              |"
	echo "| |   _________________________  |"
	echo "| |  |                        |  |"
	echo "| |  |                        |  |"
	echo "| |  |${MUSIC_LIST[$SONG_INDEX]}  "
	echo "| |  |                        |  |"
	echo "| |  |________________________|  |"
	echo "| |                              |"
	echo "| |                              |"
	echo "| |              @@@@            |"
	echo "| |           @@@ ❤︎❤︎ @@@         |"
	echo "| |          @@@@@@@@@@@@        |"
	echo "| |         @<<@@@()@@@>>@       |"
	echo "| |          @@@@@@@@@@@@        |"
	echo "| |           @@@ ||>@@@         |"
	echo "| |              @@@@            |"
	echo "| |                              |"
	echo " \|______________________________|"
	tput smam #re-enables line wrap
}

#Prints formatting, plays random song, defines a global variable with duration of current song
play_song() {
	#if there are songs in the queue
	if [ ${#QUEUE[@]} -ne 0 ]; then
		SONG_INDEX=${QUEUE[${#QUEUE[@]}-1]} #set song index to last element in queue (FIFO)
		unset 'QUEUE[${#QUEUE[@]}-1]' #remove last element from queue

	elif [ "$1" = "SHUFFLE" ]; then
		SONG_INDEX=$(($RANDOM % $NUM_SONGS)) #Get random index in range of NUM_SONGS

	else
		SONG_INDEX="$1" #user requested index
		REQUESTED_INDEX="SHUFFLE" #resets for next time
	fi

	print_ascii_art

	#Duration of current song in seconds (global variable)
	FLOAT_DURATION=$(ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 "$MUSIC_DIR"/"${MUSIC_LIST[$SONG_INDEX]}")

	#Play song file (process runs in the background)
	#mpg123 -q --no-gapless -D 0.5 --no-seekbuffer "$MUSIC_DIR"/"${MUSIC_LIST[$SONG_INDEX]}" &
	ffplay -nodisp -loglevel panic "$MUSIC_DIR"/"${MUSIC_LIST[$SONG_INDEX]}" &

	#if not replaying previous song
	if not_replay; then
		#add currently playing song to history (at the end of the array)
		HISTORY=( "${HISTORY[@]}" "$SONG_INDEX" )
	fi
}

#Prints options, times out after song ends so another one can play
show_options() {
	#If no option is provided before the song stops playing, then go to the next song
	#"TIMEOUT" is a default value; if $option still equals "TIMEOUT" after read, we know the user didn't input anything
	local option="TIMEOUT"

	echo "(n)ext, (b)ack, (q)uit, (p)ick, (l)ibrary, (s)top, (h)elp"
	
	#we only want to set seconds to 0 when we start playing a new song ($2 will be "1" if song is already playing)
	if [ "$2" == "" ]; then
   		SECONDS=0
   	fi

	read -n 1 -s -t $(($INT_DURATION - $SECONDS)) option

	# =========== OPTIONS ===========
	#Next song; OR song duration has expired
	if [ "$option" = "n" ] || [ "$option" = "TIMEOUT" ]; then
		stop_song
	#list available songs
	elif [ "$option" = "l" ]; then
		show_songs
		show_music_player
	#ask user to pick a song
	elif [ "$option" = "p" ]; then
		pick_song "Play" #Sets REQUESTED_INDEX to valid index
		stop_song
	#play previous song
	elif [ "$option" = "b" ]; then
		#Remove current song from history (unless there's only 1 song in history)
		if [ "${#HISTORY[@]}" -gt  1 ]; then
			unset 'HISTORY[${#HISTORY[@]}-1]'
		fi
		queue_previous
		stop_song
	#FOR DEBUGGING - show history
	elif [ "$option" = "1" ]; then
		echo "Length: ${#HISTORY[@]}"
		printf '%s ' "${HISTORY[@]}"
		read -t 3
		show_music_player
	#quit
	elif [ "$option" = "q" ]; then
		ctrl_c
	#download song from url
	elif [ "$option" = "d" ]; then
		download_song
		show_music_player
	#pause/resume
	elif [ "$option" = "s" ]; then
		kill -TSTP $! #gentle terminal stop signal (ctrl z) -- reversable
		let remaining=$( echo $SECONDS )
		clearscreen
		print_ascii_art
		bold; echo "(q)uit, (s)tart"; unbold
		read -n 1 resume
		if [ "$resume" = "s" ]; then
			kill -CONT $! #resume process
		elif [ "resume" = "quit" ]; then
			ctrl_c
		fi
		show_music_player "$(($INT_DURATION  - $remaining))" #don't reset elapsed time
	#loop mode
	elif [ "$option" = "r" ]; then
		toggle_repeat_mode #holds user in this function until they exit repeat mode
		show_music_player "$(($INT_DURATION - $SECONDS))" "1" #don't reset elapsed time
	#restart current track
	elif [ "$option" = "k" ]; then
		stop_song
		play_song "$SONG_INDEX"
		show_options
	#add to queue
	elif [ "$option" = "2" ]; then
		pick_song "Queue" #adds song index to queue/play next
		show_music_player "$(($INT_DURATION - $SECONDS))" "1" #don't reset elapsed time
	elif [ "$option" = "3" ]; then
		printf '%s ' "${QUEUE[@]}"
		read -t 4
		show_music_player
	elif [ "$option" = "h" ]; then
		clearscreen
		display_help_menu "full"
	elif [ "$option" = "v" ]; then
		display_help_menu "short"
	#Invalid input; show options again and do nothing
	else
		show_music_player
	fi
	# =========== /OPTIONS ===========
}
# ========== /MAIN FUNCTIONS ==========


#The script will start executing here
EXTENSIONS="*.mp3 *.pcm *.wav *.aac *.ogg *.m4a *.aif *.flac"
MUSIC_DIR=~/Downloads #default directory of audio files
setup "$@" #pass arguments to script into function

#Value is changed by show_options, pick_song, or queue_previous
#Value is reset to SHUFFLE by play_song whenever a new song is played
REQUESTED_INDEX="SHUFFLE" #Begin by shuffling music

# ============ MAIN EXECUTION LOOP ============ #
while :;
do	
	play_song "$REQUESTED_INDEX"
	INT_DURATION=$( printf "%.0f" $FLOAT_DURATION ) #converts parsed floating point value to integer for bash
	#Show options for INT_DURATION seconds (entirety of song)
	show_options "$INT_DURATION"
done