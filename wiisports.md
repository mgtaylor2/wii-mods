# Wii Sports Mods
## Helper Codes
### Master Button Activator [Bully@Wiiplaza]
```
F6000001 80008100
80BF0004 807F0008
D2000010 00000002
90DF0000 3D808000
90CC1500 00000000
E0000000 80008000
```
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
### Misc. Tennis Hacks
#### Unlimited Bounces Allowed
```
F6000001 80008100
801E01E4 540007BD
14000034 38000000
E0000000 80008000
```
#### Send Ball to Ground on Button Press
*ZZZZ = Button(s) to press to send ball to ground (fill in the same twice)*
```
F6000001 80008100
7FE5FB78 ECA20FFA
D2000008 00000004
C07E000C 3D808000
A18C1502 718CZZZZ
4182000C 3D803F40
919E000C 00000000
D2000024 00000003
3D808000 A18C1502
718CZZZZ 40820008
D07E000C 00000000
E0000000 80008000
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
### Misc. Baseball Hacks

## Boxing
### Instant KO (Player 1 + Player 2 / CPU)
```
F6000001 80008100
C006002C C026000C
D2000008 00000004
2C000000 4082000C
3D800000 48000008
3D800000 91860008
C0460008 00000000
E0000000 80008000
```
## Bowling

## Golf
### Wind Speed
```
TBD
```


----
# Wii Baseball Mapping clean up later:
## Wii Sports Baseball — Pitch Trajectory & Aim Memory Map
**Game ID:** RSPE01 | **Emulator:** Dolphin

---

### Speed (Previously Known)

| Address | Default (Hex) | Default (Float) | Description |
|---------|--------------|-----------------|-------------|
| 803C5E5C | 408AF838 | 4.343 | Fastball speed |
| 803C5E60 | 40400000 | 3.0 | Splitter speed |
| 803C5E6C | 40800000 | 4.0 | Curveball speed |
| 803C5E74 | 40800000 | 4.0 | Screwball speed |

---

### X Axis — Horizontal Aim (NEW)

| Address | Default (Float) | Description | Confirmed? |
|---------|-----------------|-------------|------------|
| 803C5E7C | 0.0 | Fastball X aim — 0 = home plate, + = right, − = left | ✅ Confirmed |

> Pattern likely continues for other pitch types nearby — needs mapping.

---

### Y Axis — Vertical Trajectory (NEW)

| Address | Default (Float) | Pitch | Input Strength | Confirmed? |
|---------|-----------------|-------|---------------|------------|
| 803C5E80 | ~0.028 | Curveball A | Light | ✅ Confirmed |
| 803C5E84 | ~-0.026 | Curveball A | Hard | ✅ Confirmed |
| 803C5E98 | ~-0.045 | Curveball B | Normal | 🔍 Hypothesized |
| 803C5E9C | ~0.040 | Curveball B | Hard | 🔍 Hypothesized |
| 803C5EA8 | ~0.01 | Fastball | Light / Normal | ✅ Confirmed |
| 803C5EAC | ~-0.039 | Fastball | Hard / Max | ✅ Confirmed |
| 803C5EB0 | ~0.01 | Splitter A+B | Light | ✅ Confirmed |
| 803C5EB4 | ~0.005 | Splitter | Hard (swap EB4/EB8?) | ⚠️ Uncertain |
| 803C5EB8 | ~0.005 | Splitter A+B | Normal (swap EB4/EB8?) | ⚠️ Uncertain |

---

### Gecko Codes

#### Moon Ball — Fastball Goes Straight Up
```
043C5EA8 42C80000
043C5EAC 42C80000
```
`42C80000` = 100.0 — light or hard input both launch vertical

---

#### Ground Worm — Fastball Buries Instantly
```
043C5EA8 C2C80000
043C5EAC C2C80000
```
`C2C80000` = -100.0 — ball drills into the ground on release

---

#### First Base Special — Fastball Throws to 1st Base
```
043C5E7C C1A00000
```
`C1A00000` = -20.0 — hard left X aim, ball goes to 1st base side

---

#### Vertical Splitter — A+B Throw Goes Straight Up
```
043C5EB0 42C80000
043C5EB4 42C80000
043C5EB8 42C80000
```

---

#### Sky Curveball — Curveball A Launches Upward
```
043C5E80 42C80000
043C5E84 42C80000
```

---

#### Chaos Pitcher — Everything Goes Wrong
*All Y axes maxed, fastball aimed hard left*
```
043C5E7C C1A00000
043C5E80 42C80000
043C5E84 42C80000
043C5EA8 42C80000
043C5EAC 42C80000
043C5EB0 42C80000
043C5EB8 42C80000
```
