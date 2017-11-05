# concert0x
## About
concert0x is a web application for midi chat with optional video and audio. The idea is that you and your partner connect your/their MIDI instruments, and now you can play a duo. Application displays 2 piano keyboards so each one can not only hear, but see what their partner is playing. Good for piano teaching or discussing musical ideas. 

Program is built on WebMidi and WebRTC standards, which are implemented in Chrome. **Only Chrome, nothing else**. Program works by establishing peer-to-peer connection between endpoints, so the delay is as short as it gets (tested between Toronto and SF, the points 3600 km apart: avg delay 20ms), it won't hinder your musical collaboration (hopefully).

App is available at https://chat.concert0x.com (no username/password required)

## MIDI setup

First off, make sure the driver corresponding to your MIDI keyboard is installed on your computer, and the device shows up in "MIDI in" menu and (if your keyboard is a synth) also in MIDI out. Otherwise, please troubleshoot and reload the page.

Select your instrument from the menu. Notice 'echo on/off' button. 'Echo on' is for instruments like dumb MIDI controllers that doesn't have built-in synth. To cover this case, application comes equipped with built-in SW synth of rather average quality, you won't emjoy it much, but it's better than the alternative (which is: nothing at all).

**IMPORTANT**: all the MIDI input of your partner will be routed right to to **your** keyboard, so the sound quality won't be affected by the noise in communication channel. 

Here are most common modes of MIDI setup:

- If you have an electric piano with built-in synth (Roland, Yamaha, Casio etc), it should show up in both "MIDI in" and "MIDI out" menus. Select it in both, and choose 'echo off' to avoid routing your own input to your own output.

- if you have dumb MIDI keyboard, select it in "MIDI in", and then select "built-in" from 'MIDI out menu'. Set 'echo on'

- if you don't want to hear your partner's playing (for some or other reason), select 'none' from MIDI out menu. But if your device is a dummy keyboard, you won't hear yourself either. This can be a preferred choice for beginner musicians.

## Logging in

The system will open a session (with unique session id) for you and print URL to send to your partner - you can use email or whatever, beyond the app. You will join the session with your partner in different roles. The organizer's role is 'piano1', and partner's - 'piano2'. There can also be a listener to your duo (just one!). This one is not supposed to have a MIDI piano at all, and even if he does, it won't have any effect whatsoever.

So piano1 should connect first, piano2 - second. Piano2 has to press 'connect' button to establish communication with piano1, who at that point is supposed to already connect. 

## Chat

For regular text messages, there's a text box down the page. If you enter text message starting with backslash character, it's interpreted as a "command", not a text message

## Video/audio

Video/audio isn't enabled by default (for your peace of mind). To enable it, add parameter to url, like v=yy or yn or ny. First letter (y/n) is for video, second - for audio. You will be requested permission to enable camera and/or microphone. Your partner should also run in video/audio mode, or else nothing will work. Piano1 can initiate call by entering "\call" command (see below).

Example url: https://chat.concert0x.com?v=yy
Another example: https://chat.concert0x.com?s=xxxxx&v=yy

## Commands

We opted for keeping UI clean and uncluttered, so instead of complex menus, there's a command like interpreter. Commands are distinguished from text messages by first symbol '\' (backslash). 

**\ping** - test latency of communication channel. Wait 10-20 seconds to see results.

**\call** - if video/audio is enabled, then piano1 can use this command to start audio/video session with piano2.

**\kb n onn/off** (n=1 or 2) - show/hide keyboard n

**\rec** - start recording

**\end** -stop recording without saving

**\save name** - stop recording and save the song under given name. Data is saved in the local store (in a browser). 

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

## Code

For now, I don't want to publish the code here in easy-to-digest form because the app is still at experimentation phase, and honestly, there's nothing much to see b/c all the heavy lifting of WebRTC is done by peer.js library. If you just want to make sure the app doesn't do anything bad, I placed concatenated sources in release directory. Though you can always hit F12 in Chrome debugger and see what is actually served.

Please note that there's a server part (see peer.js for details). I've set up my own server in the cloud, but in order to save $$$, I had to settle for a pretty basic VM instance, which can be overwhelmed if the app suddenly becomes popular. My plan, of course, is to eventually sell the whole thing to the highest bidder for $1 billion dollars plus. I'm ready to consider offers. Yes, it's the billion with the "B" (I have extenuating financial circumstances).

## Legal

You can use the source code in any way you want, all components are MIT-licensed. But when you use the app (as opposed to staring into source code), then it's free only if you swear to share your Grammy award 
## Acknowledgements

App uses peer.js library for WebRTC. Corresponding server part is ...

Built-in synth is borrowed from ...

Local store library: 

UI: jquery UI

hterm: library that I once found on github and copied, but now cannot find it (there's a project of the same name, but it doesn't look the same). Not sure.

