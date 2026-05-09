# Wii Sports Resort (USA)
## Swordplay
```
tbd
```
## Wakeboarding
```
tbd
```
## Frisbee
```
tbd
```
## Archery
```
tbd
```
## Basketball
### Zero Gravity Basketball - NOT TESTED
```
045091B0 60000000
045091B4 60000000
```
### Enhanced Basketball (removes 3 checks) - NOT TESTED
```
C2501E10 00000001
60000000 00000000
C2501E14 00000001
60000000 00000000
C2501E18 00000001
60000000 00000000
```
### Dunk Only
```
04501F64 38600000
0452DC1C 2C030000
```
### Half Court Shots (Press A after initial handoff goes WAY back but only for a little bit)
```
C2501E10 00000001
60000000 00000000
C2501E14 00000001
60000000 00000000
C2501E18 00000001
60000000 00000000
```
### CUSTOM on off dunking P1 press 1 to slam dunk before hit B, press 2 for normal shot before hit B
```
2086E024 00000200
04501F64 38600000
0452DC1C 2C030000
E0000000 80008000
2086E024 00000100
04501F64 3860FFFF
0452DC1C 2C03FFFF
E0000000 80008000
```
## Table Tennis
### Masters Ping Pong - No Friction Ball - basically makes it super bouncy
NOPs the friction calculation. Ball slides instead of decelerating.
```
0433E55C 60000000
```
### Masters Ping Pong - Heavy Friction Ball - basically makes it super bouncy
Write 2x instead of NOP
```
0406B45E4 40000000
```
### Ping Pong Crisis - Modified Ball Physics - BREAKS GAME AS IS ball goes to the moon on serve never comes back
NOPs a different check — likely ball speed/reset behavior. Combined with Masters it's chaotic.
```
0433E528 60000000
```
### WiiPlay Ping Pong - Custom Ball Behavior - NOT TESTED
This one is the most interesting — it injects 76 bytes of custom Power PC assembly at 0x80002388 in free RAM, then branches
   to it from 0x8033E5A8. It's a full custom ball physics routine mimicking Wii Play table tennis.
```
06002388 0000004C
9421FFF4 D0010000
D3E10004 7FE0FB78
83E10000 7FFFFA14
2C1F0000 4182001C
83E10000 77FF8000
3FFF392E 93E10000
C3E10000 FC00F82A
C3E10004 3821000C
7C1F0378 D003001C
4E800020 00000000
0433E5A8 4BCC3DE1
```
## Golf
```
tbd
```
## Bowling
### Bouncy Bowling - NOT TESTED
```
04376C18 60000000
```
### Bowling Speed Hack (faster ball) - NOT TESTED
```
04376C40 3C00BF80
04376C4C 90030024
```
### ImpossiBowl (max chaos) - NOT TESTED
```
044EB0D8 FFFFFFFF
```
### Masters Bowling - NOT TESTED
```
044EB008 FFFFFFFF
```
## Power Curising
```
tbd
```
## Canoeing
```
tbd
```
## Cycling
### Cycling Tornado (toggle with C button) - NOT TESTED
```
2086E024 00000400
043339AC 4E800020
CC000000 00000000
043339AC C002D3F4
E0000000 80008000
20DEEFEC 00000003
04DEEFC0 7F800000
E0000000 80008000
```
### Cycling Randomness (toggle with Z) - NOT TESTED
```
2086E024 00004000
04333814 60000000
CC000000 00000000
04333814 C06300E0
E0000000 80008000
20DEEFEC 00000003
04DEEFC0 7F800000
E0000000 80008000
```
Note: The cycling codes check address 0x80DEEFEC == 3 as a "game state is active" gate. This may or may not hold value 3 in vanilla WSR during a cycling race — if not, the heart-loss parts won't activate. The tornado/randomness toggle parts (button-based) should still work regardless.
## Air Sports
```
tbd
```
