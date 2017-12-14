# concert0x
## About
concert0x is a web application that enables midi chat between 2 parties, with optional video and audio. The idea is that you and your partner connect your/their MIDI instruments, and now you can play a duo. Application displays 2 piano keyboards so each player can not only hear, but see what the other side is playing. Good for piano teaching or discussing musical ideas. 

Program is built on WebMidi and WebRTC standards. WebMidi is implemented only in Chrome at the moment. In browsers not yet supporting WebMidi, program allows to listen to peer's playing (and recordings) via biilt-in synth only. Program works by establishing peer-to-peer connection between endpoints, so the delay is as short as it gets (tested between Toronto and SF, the points 3600 km apart: avg delay 20ms). 

App is hosted on AWS, the url is  https://chat.concert0x.com (no registration required)
For standalone exercises, go to https://chat.concert0x.com?s=standalone (handy for recording and playback of imported songs)

LIMITATIONS: by default, program uses only STUN servers to establish direct connection between peers. So if you connection fails, you are out of luck. Then you need TURN servers. If you run into this situation, please let me know - I will permanently enable TURN servers for you (TURN servers are not completely free, I have to use commercial service, but it's manageable for now).

## MIDI setup

First off, make sure the driver corresponding to your MIDI keyboard is installed on your computer, and the device shows up in "MIDI in" menu and (if your keyboard is a synth) also in MIDI out. Otherwise, please troubleshoot and reload the page.

Select your instrument from the menu. Notice 'echo on/off' button. 'Echo on' is for instruments like dumb MIDI controllers that don't have a built-in synth. To cover this case, application comes equipped with the built-in SW synth of rather average quality, you won't enjoy it much, but it's better than the alternative (which is: nothing at all).

**IMPORTANT**: all the MIDI input of your partner will be routed right to **your** keyboard, so the sound quality won't be affected by the noise in the communication channel. 

Here are most common modes of MIDI setup:

- If you have an electric piano with built-in synth (Roland, Yamaha, Casio etc), it should show up in both "MIDI in" and "MIDI out" menus. Select it in both, and choose 'echo off' to avoid routing your own input to your own output.

- if you have dumb MIDI keyboard, select it in "MIDI in", and then select "built-in" from 'MIDI out menu'. Set 'echo on'

- if you don't want to hear your partner's playing (for some or other reason), select 'none' from MIDI out menu. But if your device is a dummy keyboard, you won't hear yourself either.

## Logging in

The system will open a session (with unique session id) for you and print URL to send to your partner - you can use email or whatever, beyond the app. You will join the session with your partner in different roles. The organizer's role is 'piano1', and partner's - 'piano2'.

So piano1 should connect first, piano2 - second. Piano2 user has to press 'connect' button to actually join the session.

IMPORTANT: right now, program lacks the logic to re-connect gracefully in case of loss of connection. It's not 
possible to implement reliable mechanism of re-connections without using some out-of-band communication, though some palliative solutions exist (TODO). Right now, in case of loss of connection, both parties should reconnect, wait 5-7 seconds, then repeat (randomness will do its job). The need to re-connect should not occur very often.

## Chat

For regular text messages, there's a text box down the page. If you enter a text message starting with backslash character, it's interpreted as a "command", not a text message

## Video/audio

Video/audio call menu is available to piano1 after successful connection to piano2 is established.

## Commands

We opted for keeping UI clean and uncluttered, so instead of complex menus, there's a command line interpreter. Commands are distinguished from text messages by first symbol '\\' (backslash).

When you work with songs, dot passed as song name stands for the last used song name (e.g. `\play .`)

**\ping** - test latency of communication channel. Wait 10-20 seconds to see results.

**\kb n onn/off** (n=1 or 2) - show/hide keyboard n

**\rec** - start recording

**\end** -stop recording without saving

**\save name** - stop recording and save the song under given name. Data is saved in the local store (in a browser). 

**\play name [tempoFactor]** - play recorded song in a different tempo. Good for recording virtuosic pieces for showing off when you can't actually play virtuosically. Unexplored territory. Killer feature of the app. (My own experiments suggest that values of about 1.3 work best, then the piece starts losing authenticity)

**\ls** - list recorded songs

**\mv name1 name2** - rename song

**\cp name1 name2** - copy song under different name

**\rm name** - remove song

**\export name** - copies song to clipboard as text in a certain format (details don't matter). You can save it in a file or send to somebody for importing. I recommend saving in dropbox and obtaining a public URL.

**\import name encodedSong**  - take output of export and paste it in this command as encodedSong. Song will be imported under given name. If you place your song in a txt file in dropbox, you can use public URL as a second argument.

**\setup session** - generate a new session. Yes, you need to type the word 'session'. To be used to set up different sessions with different partners. (e.g., when communicating with Bob, my session id is xxx, and for Alice - yyy)

## Keyboard shortcuts

You can use the pitch wheel as a "control" for a program. Rotate a wheel, hit note C, then release the wheel. This will be interpreted as "\rec" command. Here's the list of shortcuts:

- C : `\rec`
- D : `\save` (save song under generated name)
- C# : `\play .` (play last recorded song) 
- D#:  `\play . 2` (play last recorded song in double tempo)
- F: `\end` (end recording without saving)
- F#: `stop` (stop playback)

## Code

For now, I don't want to publish the code here in easy-to-digest form because the app is still at experimentation phase, and honestly, there's nothing much to see b/c all the heavy lifting of WebRTC is done by peer.js library. You can always hit F12 in debugger and see what code is actually served (no obfuscation was applied)

Please note that there's a server part (see peer.js for details). I've set up my own server in the cloud, but in order to save $$$, I had to settle for a pretty basic VM instance, which can be overwhelmed if the app suddenly becomes overly popular.  

## Legal

The program and all components are MIT-licensed. But if you use the cloud-based app repeatedly, please find a way to contribute to the project.

## Acknowledgements

App uses [peer.js](https://github.com/peers/peerjs) library for WebRTC. Corresponding server part can be found [here](https://github.com/peers/peerjs-server)

Built-in synth is borrowed from [soundfont-player](https://github.com/danigb/soundfont-player)

Local store library: [xstore](https://github.com/niiknow/xstore) 

UI: [jquery UI](https://jqueryui.com)

hterm: library that I once found on github and cloned, but now cannot find it (there's a project of the same name, but it doesn't look the same). Not sure.

