# mdsysexgen
##### Generates Machinedrum-formatted Sysex messages

The Elektron Machinedrum takes in Sysex messages formatted as:
```$f0,$00,$20,$3c,$02,$00,command,...,$f7```
To set the tempo, the computer expects the ```command``` portion to read...
```
$61 | Set tempo command ID
%0aaaaaaa | Upper bits
%0bbbbbbb | Lower bits
$f7 | SYSEX end
Note: Tempo = %aaaaaaabbbbbbb / 24, max 300 BPM, min 30 BPM
```

So Supercollider, for instance, can create and send a valid set tempo sysex command...

```
MIDIClient.init;
m = MIDIOut(0);  // Linux users: MIDIOut(0, MIDIClient.destinations[0].uid)
m.sysex(Int8Array[0xf0, 0x00, 0x20, 0x3c, 0x02, 0x00, 0x61, 21, 54, 0xf7]);
// This will set the tempo to 114.23 bpm. One can calculate the upper and lower 7bit values like this...
(
var bpm, val, upper, lower;
bpm = 114.23;
val = (bpm*24).round.asInteger;
upper = val&2r11111110000000>>7;
lower = val&2r00000001111111;
[upper, lower].postln;
)
```
where the resulting 21 and 54 are the same as 2r0010101 and 2r0110110 in binary notation.
