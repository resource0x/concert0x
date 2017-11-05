# concert0x
## About
concert0x is a web application for midi chat with optional video and audio. The idea is that you connect your MIDI instrument, your partner connects their instrument, and you may play a duo. Application displays 2 piano keyboards so each one can not only hear, but see what their partner is playing. Good for piano teaching or discussing musical ideas. 

Program is built on WebMidi and WebRTC standards, which are implemented in Chrome. **Only Chrome, nothing else**. Program works by establishing peer-to-peer connection between endpoints, so the delay is as short as it gets (tested between Toronto and SF, points 3600 km apart: avg delay 20ms), it won't hinder your musical collaboration (hopefully).

## Logging in
go to https://chat.concert0x.com

The system will open a session (with unique session id) for you and print URL to send to your partner - you can use email or whatever, beyond the app. You will share the session with your partner in different roles. The organizer's role is 'piano1', and partner's - 'piano2'. There can also be a listener to your duo (just one!). This one is not supposed to have a MIDI piano at all, and even if he does, it won't have any effect whatsoever.

So piano1 should connect first, piano2 - second. Piano2 has to press 'connect' button to establish communication with piano1, which at that point is already connected. Please select your instrument from the menu, notice 'echo on/off' button - and start playing. Echo on is for instruments like MIDI controller that doesn't provide synth. Application comes with built-in SW synth of rather average quality, you won't emjoy it much, but it's better than the alternative (which is: nothing at all).


## Q/A
What happens if your partner doesn't have a keyboard? Fine, he/she can just listen to whatever you are playing.
