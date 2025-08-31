# konversation-mpd - an mpd script for the Konversation IRC client

An mpd interface for Konversation. Functions as a scrobbler/announcer for a window, as well as allows control of MPD by passing commands.

## Requirements

- mpd
- bc

mpd is obvious. bc is used to do math on sample rates.

## Installation

- Make any changes to the formatting of the output you wish, and copy the script to wherever Konversation looks for scripts on your system.
- Assign `/exec mpdirc` to an alias of your choosing.

## Usage

The script functions as both a ~~spa~~announcer and as an MPD client. We'll assume you assigned it to `/mpd` in Konversation.

### Playback Announcer

Simply feeding `/mpd` will ~~spa~~announce your plaback information to the current window.

`/me mpd: Song Title - Artist (From: Album) [ elapsed/duration | audioformat | bitrate | codec]`

`[Paused]` will be appended to the song title if in paused state. `mpd: Not Playing` will be shown if in stop state.

The format in which this is sent can be modified in the script itself. Simply rearrange and/or remove information you don't want. A list of variables are provided in this readme in case you want to add something.

### MPD client

The script can function as a "full" client by feeding commands/arguments to `/mpd`. Anything you send to the script will be passed to mpd as a command.

```
play [position] - Starts playback. Optional: Position to start playback at. Otherwise, uses current position.
pause           - Toggles Pause. 
next            - skip track
previous        - go back a track

```
These are just the commonly used examples. A full set of commands are available in mpd's command reference. If the command fails, the mpd error is returned to the chat window. If you want to type out the stupid long command to add a file to your playlist; it will take it and pass it.

But it's mostly to be able to go `/mpd next`.

## How It Works

The script talks to mpd (directly) using a file-handler to /dev/tcp/localhost/6600. After dropping it's banner response, we send it the `status` and `currentsong` commands; mpd responds with a ton of fields. We convert all of those fields in to a variable=value. We then check if the format is DSD or a sample rate and either specify a rate or divide by 1000. The extension of the file is obtained and some logic is done to determine which codec information we're going to display. We slap all of this in to a variable formatted the way we want; and send it to koversation over dBus.

Arguments passed to the script beyond Konversation's server and target are passed directly to mpd as a client command.

## TODO

- ~~List of variables, in case there's something you want I'm not already listing.~~
- ~~Playback controls: start, stop, pause, skip...all from within IRC.~~
- Use, test, fix edge cases.

## CHANGES

```
30-AUG-2025: Initial Release. Only announcer.
31-AUG-2025: Stop/Pause states. Added remote/client features.
```

## Variables

Since mpd reports things in the `name: value` format, it's easy to convert everything in to a script variable. As a result we pull in some additional information that's not used; whether any of it is useful I don't know. I'm just getting a full look at this list myself. 

```
repeat=0
random=0
single=0
consume=0
partition=default
playlist=14
playlistlength=12
mixrampdb=0
state=play
song=0
songid=1
time=65:176
elapsed=64.992
bitrate=5644                # kbps
duration=176.360
audio=44100:16:2 (dsdxx:2)  # DSD content does not report a bit-depth, and it reports it's DSD rate as sample rate. Please don't use this directly.
nextsong=1
nextsongid=2
file=Path/To/File.ext
Added=2014-10-31T00:26:24Z
Track=1                     # This is album track title.
Date=2014
Album=Album Title
Artist=Artist
Title=Track Title
Performer=Performer
Time=176
duration=176.360
Pos=0
Id=1
```

Additionally, here are some script-specific variables you can use in the output:

```
audio_result    - This generates samplerate khz/bitdepth-bit/stereo|format_part. It divides the sample rate by 1000, formats it from : to /, and it
                  also handles the DSD formatting. Inserts the MHz sample rate and appends the DSD format_part if it exists.
format_part     - The audio format portion of audio_result containing sample rate, bith depth, and stereo.
codec_result    - This determines what to show for codec, capitialized file extension or DSD stuff. Also reports Super Audio CD if ISO is detected.
```
