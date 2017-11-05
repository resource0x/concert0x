# concert0x
## About
concert0x is a web application for midi chat with optional video and audio. The idea is that you and your partner connect your/their MIDI instruments, and now you can play a duo. Application displays 2 piano keyboards so each one can not only hear, but see what their partner is playing. Good for piano teaching or discussing musical ideas. 

Program is built on WebMidi and WebRTC standards, which are implemented in Chrome. **Only Chrome, nothing else**. Program works by establishing peer-to-peer connection between endpoints, so the delay is as short as it gets (tested between Toronto and SF, points 3600 km apart: avg delay 20ms), it won't hinder your musical collaboration (hopefully).

App is available at https://chat.concert0x.com

## MIDI setup

First off, make sure the driver corresponding to your keyboard is installed on your computer, and the device shows up in "MIDI in" menu and (if your keyboard is a synth) also in MIDI out. Otherwise, please troublishoot and reload the page.

Select your instrument from the menu. Notice 'echo on/off' button. 'Echo on' is for instruments like dumb MIDI controllers that doesn't have built-in synth. To cover this case, application comes equipped with built-in SW synth of rather average quality, you won't emjoy it much, but it's better than the alternative (which is: nothing at all).

**IMPORTANT**: all the MIDI input of your partner will be routed right to to **your** keyboard, so the sound quality won't be affected by the noise of communication channel. 

Here are most common modes of MIDI setup:

- If you have an electric piano with built-in synth (Roland, Yamaha, Casio etc), it should show up also in both "MIDI in" and "MIDI out" menus. Select it in both, and choose 'echo off' to avoid routing your own input to your own output.

- if you have dumb MIDI keyboard, select it in "MIDI in", and then select "built-in" from 'MIDI out menu'. Set 'echo on'

- if you don't want to hear your partner's playing (for some or other reason), select 'none' from MIDI out menu. But if your device is a dummy keyboard, you won't hear yourself either. This can be a preferred choice for beginner musicians.

## Logging in

The system will open a session (with unique session id) for you and print URL to send to your partner - you can use email or whatever, beyond the app. You will share the session with your partner in different roles. The organizer's role is 'piano1', and partner's - 'piano2'. There can also be a listener to your duo (just one!). This one is not supposed to have a MIDI piano at all, and even if he does, it won't have any effect whatsoever.

So piano1 should connect first, piano2 - second. Piano2 has to press 'connect' button to establish communication with piano1, which at that point is already connected. 

## Chat

For regular text messages, there's a text box down the page. If you enter text message starting with backslash character, it's interpreted as a "command", not a text message

## Commands

We opted for keeping UI clean and uncluttered, so instead of complex menus, there's a command like interpreter. Commands are distinguished from text messages by first symbol '\' (backslash). 

**\ping** - test latency of communication channel. Wait 10-20 seconds to see results.

**\call** - if video/audio is enabled, then piano1 can use this command to start audio/video session with piano2.

**\kb n onn/off** (n=1 or 2) - show/hide keyboard n

**\rec** - start recording

**\end** -stop recording without saving

**\save name** - stop recording and save the song under given name 

**\play name [tempoFactor]** - play recorded song in a different tempo. Good for recording virtuosic pieces for showing off when you can't actually play virtuosically. Unexplored territory. Killer feature of the app.

**\ls**" - list recorded songs

**\mv oldName newName** - rename song

**\cp src dst** - copy song under different name

**\rm name** - remove song

**\export name** - prints song as text in a certain format (details don't matter). Copy it and send to somebody or save for later use.

**\import name encodedSong**  - take output of export and paste it in this command as encodedSong. Song will be imported under given name.

**\setup** - set up a new session (not sure why you need it)

**\audio on/off** -enable/disable audio. only works if you run in multimedia mode (v=yy or yn or ny - see above)

**\video on/off** -enable/disable video. only works if you run in multimedia mode (v=yy or yn or ny - see above).

**\chan** - I don't remember what it's for

## Q/A
What happens if your partner doesn't have a keyboard? Fine, he/she can just listen to whatever you are playing.
