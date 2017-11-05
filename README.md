# concert0x
## About
concert0x is a web application for midi chat with optional video and audio. The idea is that you connect your MIDI instrument, your partner connects their instrument, and you may play a duo. Application displays 2 piano keyboards so each one can not only hear, but see what their partner is playing. Good for piano teaching or discussing musical ideas. 

Program is built on WebMidi and WebRTC standards, which are implemented in Chrome. **Only Chrome, nothing else**. Program works by establishing peer-to-peer connection between endpoints, so the delay is as short as it gets (tested between Toronto and SF, points 3600 km apart: avg delay 20ms), it won't hinder your musical collaboration (hopefully).

Login page: https://chat.concert0x.com

## MIDI setup

First off, make sure the driver corresponding to your keyboard is installed on your computer, and the device shows up in "MIDI in" menu and (if your keyboard is a synth) also in MIDI out. Otherwise, please troublishoot and reload the page.

Select your instrument from the menu. Notice 'echo on/off' button. 'Echo on' is for instruments like dumb MIDI controllers that doesn't have built-in synth. To cover this case, application comes equipped with built-in SW synth of rather average quality, you won't emjoy it much, but it's better than the alternative (which is: nothing at all).

**IMPORTANT**: all the MIDI input of your partner will be routed right to to **your** keyboard, so the sound quality won't be affected by the noise of communication channel. 

Here are most common modes of MIDI setup:

- If you have an electric piano with built-in synth (Roland, Yamaha, Casio etc), it should show up also in both "MIDI in" and "MIDI out" menus. Select it in both, and choose 'echo off' to avoid routing your own input to your own output.

- if you have dumb MIDI keyboard, select it in "MIDI in", and then select "built-in" from 'MIDI out menu'. Set 'echo on'

- if you don't want to hear your partner's playing (for some or other reason), select 'none' from MIDI out menu.But if your device is a dummy keyboard, you won't hear yourself either. This can be a preferred choice for beginner musicians.

## Logging in



The system will open a session (with unique session id) for you and print URL to send to your partner - you can use email or whatever, beyond the app. You will share the session with your partner in different roles. The organizer's role is 'piano1', and partner's - 'piano2'. There can also be a listener to your duo (just one!). This one is not supposed to have a MIDI piano at all, and even if he does, it won't have any effect whatsoever.

So piano1 should connect first, piano2 - second. Piano2 has to press 'connect' button to establish communication with piano1, which at that point is already connected. 

## Chat

As soon as the connection is established


## Q/A
What happens if your partner doesn't have a keyboard? Fine, he/she can just listen to whatever you are playing.
