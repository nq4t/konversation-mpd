# konversation-mpd - an mpd script for the Konversation IRC client

Currently only spams an IRC channel (or msg window) with some stats about what mpd is currently playing using whatever shortcut you assign it to. You can customize the output sent by editing the script; however I haven't generated a list of currently available variables.

Future plans include doing nice things if you're not in playback mode; as well as basic playback control from within Konversation.

## Requirements

- mpd
- bc

mpd is obvious. bc is used to do math on sample rates.

## Installation/Usage

- Make any changes to the formatting of the output you wish, and copy the script to wherever Konversation looks for scripts on your system.
- Assign `/exec mpdirc` to an alias of your choosing.
- Start playback in mpd.
- Issue your alias in the channel you wish to spam.
- Annoy your friends!

That's it. 

## How It Works

The script talks to mpd (directly) using a file-handler to /dev/tcp/localhost/6600. After dropping it's banner response, we send it the `status` and `currentsong` commands; mpd responds with a ton of fields. We convert all of those fields in to a variable=value. We then check if the format is DSD or a sample rate and either specify a rate or divide by 1000. The extension of the file is obtained and some logic is done to determine which codec information we're going to display. We slap all of this in to a variable formatted the way we want; and send it to koversation over dBus.

## TODO

- List of variables, in case there's something you want I'm not already listing.
- Playback controls: start, stop, pause, skip...all from within IRC.
