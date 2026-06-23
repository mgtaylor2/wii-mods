# Mario Kart Wii — 8-Player All-Dolphin LAN Party

Goal: 8 humans racing the same MKWii (ideally **Insane Kart Wii**, 5,000+ custom
tracks) on a network of MacBooks, no real Wii required. Real Wii Remotes preferred,
GameCube controllers as the safe fallback.

This doc captures the verified architecture, the dead-ends to avoid, the phased
build plan, and the Prime Day shopping list.

---

## TL;DR — the one thing that matters

**Dolphin Netplay caps at 4 players for MKWii. To get 8 you do NOT use Netplay.**
You run **multiple independent Dolphin instances, each acting as its own "console"
on the LAN**, all joined through MKWii's in-game **LAN Multiplayer mode** (loaded
via **BrainSlug**). Each console = 2 split-screen players. 4 consoles × 2 = **8**.

This is architecturally identical to the well-known 8-player GameCube Double Dash
setup (4 emulated consoles, each with an emulated Broadband Adapter, networked
together). MKWii's equivalent of the BBA-LAN path is the **BrainSlug LAN mod**.

- LAN mod / BrainSlug: <https://github.com/calvinhendriks/MKW-LAN-Brainslug> (up to 12 players)
- LAN Multiplayer wiki: <https://wiki.tockdom.com/wiki/LAN_Multiplayer>
- Insane Kart Wii: <https://wiki.tockdom.com/wiki/Insane_Kart_Wii> · <https://gamebanana.com/mods/531066>

---

## Why NOT Dolphin Netplay (the trap)

Netplay is the obvious-looking choice and it's a dead end for 8 players:

- Netplay makes every machine appear to the game as **one shared console**.
- MKWii local/offline multiplayer is **hard-capped at 4 players** (4-way split-screen).
- Netplay itself only exposes **4 controller slots**.

So Netplay tops out at **4 players, full stop** — no number of MacBooks or VMs
changes that. Netplay is the *low-effort path if 4 players is acceptable*, nothing
more. (Source: <https://dolphin-emu.org/docs/guides/netplay-guide/>)

The "MKWii supports 12 players" figure you'll see refers to the old **Nintendo WFC
online servers**, not local/Netplay play.

---

## The verified architecture (LAN mod, multi-instance)

Each Dolphin instance = one emulated "Wii" = its own network identity = up to 2
local players. Reaching 8 means **4 network nodes**.

Available hardware: Apple-Silicon MacBook Pro ("good MBP"), Intel MacBook Pro,
MacBook Air, plus one already-homebrewed real Wii in reserve.

| Node | Machine | Players | Notes |
|------|---------|---------|-------|
| 1 | Good MBP (native instance) | 2 | Its real LAN IP |
| 2 | Intel MBP | 2 | Single instance only — Intel is the weak link |
| 3 | MacBook Air | 2 | Single instance |
| 4 | **Good MBP (2nd instance, VM)** OR **real Wii** | 2 | See "4th node" below |

### Two raw instances on one Mac = IP collision

The LAN protocol keys on **IP:port**. Two plain Dolphin instances on the same Mac
share one IP and collide. Fixes, in order of preference:

1. **Separate physical machine / the real Wii** — cleanest, distinct IP for free.
2. **VM with bridged networking** on the good MBP — gives the 2nd instance its own
   IP. This is the Double Dash crowd's approach. Needs an **M-Pro/Max-class chip**
   to run 2 MKWii instances at once; risky on a base M-chip.
   - Apple-Silicon VM USB passthrough is fiddly (UTM/QEMU buggy; Parallels only
     added it in 20.3.0, 2025). Test early.
3. **TAP / loopback virtual-NIC trick** — what the multi-instance Double Dash guides
   use to give same-box instances distinct addresses. More setup, no hypervisor.

### Multiple instances cleanly: the `-u` flag

Run each instance with its own user directory so configs/controllers don't fight:

```
/Applications/Dolphin.app/Contents/MacOS/Dolphin -u ~/dolphin_A
/Applications/Dolphin.app/Contents/MacOS/Dolphin -u ~/dolphin_B
```

(Or set `DOLPHIN_EMU_USERPATH`.) Docs:
<https://dolphin-emu.org/docs/guides/controlling-global-user-directory/>

---

## Controllers — Wii Remotes vs GameCube (HEDGE)

Decision: **buy both**, try Wiimotes first, keep GC as a zero-scramble fallback.

### ⚠️ DolphinBar Mode 4 is a Windows-first device — macOS risk

On Windows, Dolphin reads the Mayflash DolphinBar's Wiimotes cleanly in **Mode 4**.
On **macOS** Dolphin tends to fall back to direct Bluetooth discovery (the Wiimotes
are already paired to the bar), which is flaky. Two bars on one Mac, pinned to
specific instances, is even less certain (identical bars enumerate identically).
**This is the #1 risk — prototype it on day one. Do not assume it works.**

- Use **official Nintendo Wiimotes only** (3rd-party don't transmit motion through the bar).
- The DolphinBar **cannot** do Bluetooth passthrough (doesn't expose its BT adapter).

### Wiimote fallback that may dodge the bar entirely

MKWii's **Wii Wheel steering is tilt/accelerometer — no IR pointing needed**. So you
can try pairing Wiimotes straight to each Mac's **native Bluetooth** (drop the
DolphinBar, no sensor bar required) and navigate menus with the d-pad held sideways.
macOS native Wiimote BT is finicky but worth testing as Plan B.

### GameCube controllers = the deadline-safe path

USB GC controllers + a Wii U/Switch GC adapter have **first-class macOS support**,
auto-detect per adapter (trivial per-instance assignment), and sidestep the whole
Bluetooth mess. You lose motion steering. Keep these in your back pocket.
Guide: <https://dolphin-emu.org/docs/guides/how-use-official-gc-controller-adapter-wii-u/>

---

## Critical rules for ANY multi-Dolphin MKWii setup

- **Byte-identical everything.** Same Dolphin build, same game dump (region AND
  revision), and — for Insane Kart Wii — the **same pre-patched build / SD payload**
  on every node. Differing track files = guaranteed desync. Prefer a shared
  extracted-folder/WBFS build over live Riivolution patching.
- **Same screen mode on all nodes** (all split-screen) or it desyncs.
- **Identical Wii Remote attachment** on all nodes (everyone Wheel, or everyone
  sideways) — mismatched extensions desync.
- **Disable the emulated SD card** during play (known Wii desync source).
- **Use a recent Dolphin build** — older Apple-Silicon builds had an MKWii desync bug
  (fixed via AArch64 JIT rounding fixes).
- **Wired networking.** USB-C→Ethernet on each Mac into a switch. Wi-Fi jitter causes
  desyncs in real-time peer traffic.

---

## Performance reality check

- A single MKWii instance at 60fps is trivial for any M-series chip.
- **Two instances at once** on the good MBP is realistic on **M-Pro/Max**, not
  guaranteed on a base M1/M2 — test thermals and framerate before relying on it.
- The **Intel MBP is the weak link**: one instance at 1080p (1.5–2× internal res) is
  probably fine but expect fans/dips; it will NOT take a second instance. Benchmark
  the actual machine.

---

## Phased build plan (1–2 week deadline)

### Phase 0 — Day-one risk burn-down (do these FIRST)
1. **Controller input path on macOS** — plug in a DolphinBar, try to get 2 Wiimotes
   live in Dolphin on a Mac. If it fights you, test native-BT pairing, else fall
   back to GC adapter. This is the biggest unknown.
2. Confirm a recent Dolphin build runs MKWii / Insane Kart Wii at full speed on each
   machine (especially the Intel MBP).

### Phase 1 — Get 6 players rock-solid (3 Macs, 3 nodes)
3. Install the **identical** Dolphin build + identical Insane Kart Wii build on all
   three Macs.
4. Set up **BrainSlug + MKW LAN Multiplayer** payload identically on each.
5. Wire all three into the switch; bring up a LAN lobby; race 6-player end to end.
6. Lock in the desync checklist above until a full GP runs clean.

### Phase 2 — Jump to 8 (pick after Phase 1 succeeds)
7. Either add the **real Wii** as node 4 (simplest, Wiimotes "just work" on it), OR
   bring up a **2nd instance in a VM** on the good MBP for its own IP. Decide based
   on how the good MBP handled load in Phase 1.

---

## Prime Day shopping list

| Item | Purpose | Notes |
|------|---------|-------|
| 1× Mayflash DolphinBar | 2nd bar for Wiimotes | You have one already; ~$20 |
| 1× GC controller adapter + controllers | Deadline-safe fallback | Hedge per decision |
| 3× USB-C → Ethernet adapter | Wired LAN per Mac | MacBooks have no Ethernet port |
| Ethernet cables | One per node | Cat5e+ fine |
| 8-port unmanaged switch | Tie nodes together | TP-Link TL-SG108, ~$20 |
| Projector (game mode, <30ms lag) | Big-screen display | Disable motion smoothing |
| 4-input HDMI multiviewer (optional) | 4-up grid on one projector | ~$50–80; or just mirror one node |

(Display note: all-Dolphin machines output HDMI directly — no Wii-specific
ElectronWarp/component chain needed unless the real Wii joins as node 4.)

---

## Sources

- Dolphin Netplay Guide (4-player cap) — <https://dolphin-emu.org/docs/guides/netplay-guide/>
- `-u` user directory flag — <https://dolphin-emu.org/docs/guides/controlling-global-user-directory/>
- GC adapter on macOS — <https://dolphin-emu.org/docs/guides/how-use-official-gc-controller-adapter-wii-u/>
- MKW-LAN-Brainslug — <https://github.com/calvinhendriks/MKW-LAN-Brainslug>
- LAN Multiplayer (Tockdom) — <https://wiki.tockdom.com/wiki/LAN_Multiplayer>
- Insane Kart Wii (Tockdom) — <https://wiki.tockdom.com/wiki/Insane_Kart_Wii>
- Insane Kart Wii (GameBanana) — <https://gamebanana.com/mods/531066>
- Dolphin on M1 — <https://dolphin-emu.org/blog/2021/05/24/temptation-of-the-apple-dolphin-on-macos-m1/>
- DolphinBar / Bluetooth Passthrough — <https://dolphin-emu.org/blog/2016/10/24/bluetooth-passthrough/>
