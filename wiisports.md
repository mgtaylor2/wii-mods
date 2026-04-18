# Wii Sports Mods
## Tennis
### Generic Info / Memory Address Mapping
* 8038B264 = tennis player speed running
* 8038b268 = unknown / minimal
* 8038b260 = unknown / minimal
* 8038b25c = unknown / minimal
* 8038b280 = maybe gravity related??
* 8038b28c = y axis for sure ball goes super high on lobs not even making it over the net
* 8038b290 = Player 1 partner by net allowed to go anywhere on the map > like 5 scalar??  originally 0
* 8038b258 = Player 2 partner by net allowed to go anywhere on the map > like 5 scalar?? originally 0
* CPU Speed = 8038b22c

### Tennis Fast Player + CPU + Modded Ball Speed
* CPU speed = 8038B22C 
* Player speed = 8038B264
* x-axis ball bounce return speed = 8038B26C mem address
```
0438B22C 40400000
0438B264 40400000
0438B26C 3fc00000
```
### Tennis Post-bounce Ball Speed Accel (X)
* x-axis = 8038B26C mem address
```
0438B26C 40C00000
```
### Tennis Movement Speed 2x
```
0438B264 40400000
```
## Baseball
### Info / Memory Mapping
* 803C5E5C = fastball
* 803C5E60 = splitter
* 803C5E6C = curveball
* 803C5E74 = screwball
* 803C5EAC = physics modifier (boost / decay tweak)
### Wii Baseball FAST Pitch Edition
Everything is fast!
```
043C5E5C 411CCCCC
043C5E60 411CCCCC
043C5E6C 411CCCCC
043C5E74 411CCCCC
043C5EAC BD6F9DB2
```
### Fly Ball Defense
Limitations: Ground ball defense is worse.
* Note: 150 (43160000) initially 5.0 (40A00000) - Extended glove grab
* Note: 25 (41C80000) initially 5.0 (40A00000) - Improved accuracy
```
044BE680 43160000
044BE17C 41C80000
```
### Original Extreme Fastball
* Note: 9.8 (411CCCCC) originally 4.34 (408AF838)
* Note: -.0585 (BD6F9DB2) originally -0.039 (BD23D70A)
* Note: 9.7 (411B3333) = 206-210mph
* Limitations: Ball sometimes goes out of bounds, causing the defensive players to dance around.
* Limitations: Wait a few seconds and the game will continue.
```
043C5E5C 411CCCCC
043C5EAC BD6F9DB2
```
