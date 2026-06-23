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
- 4 Dolphin instances + 8 controllers on one PC (forum) — <https://forums.dolphin-emu.org/Thread-4-dolphin-instances-and-4x-mario-kart-on-one-computer-playable-with-8-controllers>

---

## Single-PC Prototype — TAP adapters (the $0 go/no-go test)

**Why this section exists:** the original plan assumed distinct IPs require separate
machines or VMs (and VMs hit a GPU-virtualization wall — a single consumer GeForce
can't give real 3D acceleration to 4 guests; no SR-IOV/vGPU, and full passthrough is
one-VM-only). There is a **third way that needs zero extra hardware**: **TAP virtual
network adapters**. You create several software NICs on one Windows box, bridge them,
and give each Dolphin instance its own virtual IP/MAC via the emulated **Broadband
Adapter (BBA)**. Four *native* instances then share the one real GPU automatically —
no VMs, no GPU wall, no Ethernet adapter, no switch, and **not touching the apartment
network at all** (which has client isolation that kills LAN broadcast anyway).

### ⚠️ Proven vs. unverified — read this first

- **PROVEN (GameCube):** the forum guide below gets **4 networked instances + 8
  controllers** on one PC. But it configures **GameCube → SP1 → Broadband Adapter**
  and troubleshoots finding *"GameCube systems via LAN."* That setup is **Mario Kart:
  Double Dash**, which has *native* GC BBA LAN — hence "no BrainSlug needed."
- **UNVERIFIED (MKWii):** MKWii is a **Wii** title. It needs **BrainSlug** for LAN,
  and Wii networking in Dolphin does **not** obviously route through the per-instance
  GC BBA/SP1 TAP path. So whether 4 MKWii+BrainSlug instances bind to 4 separate TAP
  adapters is **the open question this prototype must answer.** Do not assume it works
  until the discovery test below passes with MKWii specifically.
- **Fallback if MKWii won't bind to TAP:** prototype with **Mario Kart: Double Dash**
  (proven path) to validate the rig, then fall back to **separate physical machines**
  for the real MKWii party (the broadcast-on-flat-L2 topology is known-good).

### Prerequisites (all free)

- Windows 11 (Pro not required for this — it's all native, no Hyper-V).
- One Dolphin build, one MKWii dump (+ BrainSlug LAN SD payload) for the MKWii test,
  and/or a Double Dash dump for the proven GC test.
- **OpenVPN installer** (we only use its TAP-Windows V9 driver) — <https://openvpn.net/community-downloads/>
- `VC_redist.x64.exe` (Visual C++ runtime) installed.
- Any USB controllers for input — **no DolphinBars needed for this test.**

### Part 1 — Create the TAP adapters

1. Install OpenVPN, ticking **only** the **TAP Virtual Ethernet Adapter** component.
   This creates **one** `TAP-Windows Adapter V9` by default.
2. Start menu → run **"Add a new TAP virtual ethernet adapter"** **3 more times** so
   you end up with **4** `TAP-Windows Adapter V9` connections total. (Start with 2 if
   you just want the minimal discovery test, then scale up.)

### Part 2 — Bridge them

3. Open **Control Panel → Network Connections** (`ncpa.cpl`).
4. Select **all 4** TAP adapters (**Ctrl + left-click** each), right-click →
   **"Bridge Connections."** An error may flash, but a **Network Bridge** should
   appear when it finishes. Open its properties and confirm all 4 adapters are listed.
   *(Bridging puts them on one L2 segment so LAN broadcast/discovery propagates.)*

### Part 3 — Four portable Dolphin instances

5. In your Dolphin folder, create an **empty file named `portable.txt`** next to
   `Dolphin.exe` (this makes Dolphin store its config in its own folder).
6. **Copy the entire folder 3 times** → `Dolphin_1`, `Dolphin_2`, `Dolphin_3`,
   `Dolphin_4`. Each is now fully independent (no shared config).

### Part 4 — Per-instance Dolphin config

For **each** instance, in **Settings → GameCube → SP1 slot**:

7. Set the device to **Broadband Adapter (TAP)** and enable it.
8. Give each instance a **unique MAC address**:
   - `Dolphin_1` → `00:00:00:00:00:01`
   - `Dolphin_2` → `00:00:00:00:00:02`
   - `Dolphin_3` → `00:00:00:00:00:03`
   - `Dolphin_4` → `00:00:00:00:00:04`
9. In **Controllers**, enable **Background Input** so instances keep reading their
   pads when not focused.

### Part 5 — Controllers (8 across 4 instances)

10. **XInput hard-caps at 4 controllers.** To exceed 4, use **DInput-type** pads
    (e.g. PlayStation controllers). Wire them via **USB**, not Bluetooth.
11. Assign **2 controllers per instance** (slots active per instance; the others off),
    so 4 instances × 2 = 8 players. *(DolphinBar/Wiimote routing is a later problem —
    not needed to prove networking.)*

### Part 6 — Launch and the discovery test (the actual go/no-go)

12. Launch all instances (start with **2** to keep it simple), boot the game in each.
    For **MKWii**, load it through **BrainSlug** and choose **LAN Multiplayer**; for the
    **Double Dash** proof-of-rig, just pick **LAN** in-game.
13. Select **LAN play** on each instance. After a moment a **countdown** starts and the
    per-instance connection indicators should clear their **red X**.
14. ✅ **Success = the game reports the other systems found for LAN play** (e.g. "N
    systems were found"). That single screen is the entire viability answer.

### Troubleshooting

- **No systems found?** Drop to **2** TAP adapters bridged and retest. If 2 work, add a
  3rd to the bridge and retest, then the 4th — isolates which adapter/bridge step broke.
- **Bridge "failed" message** but a bridge still appears → usually fine, continue.
- **MKWii finds nothing but Double Dash does** → this is the unverified-path risk firing:
  MKWii's Wii network stack isn't using the TAP BBA. Fall back to separate machines for
  the real MKWii party; keep the single-PC TAP rig for GC titles.
- **Wi-Fi vs wired:** the mod doesn't care about the medium for *discovery* — wired is a
  *desync/jitter* requirement for the real event, not a protocol one. (TAP sidesteps this
  entirely on one box: there's no physical link to jitter.)

### What this buys you

If the MKWii discovery test passes here, the whole party can run on the **one gaming PC**
(5800X3D + RTX 4080 Super) with **native instances sharing the GPU** — no VMs, no
GPU-P, no second machine, and **DolphinBars become the only remaining purchase to
evaluate** (and only *after* networking is proven). If it fails for MKWii, you've spent
**$0** learning that, and the separate-machines plan above is the fallback.
