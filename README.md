# Tether Multiplayer Movement Document
**Tether Multiplayer Movement Plugin Usage Guide**

---

## Overview
This plugin provides two complementary systems for UE5 multiplayer projects:

- **Client-Authoritative Movement** with server-side LERP smoothing for very responsive local control and reduced jitter.  
- **Player Tether System** rope-like connections between players with configurable elasticity and pull strength for cooperative mechanics.

---

## Quick Start (Recommended)
1. Make **`BP_MyTetherCharacter`** the parent of your character blueprint (this wires up most functionality automatically).
2. In your character, **add the Movement Replication Component** if you want only the movement system.
3. **Disable** the character’s **`Replicate Movement`** option (so the plugin handles replication).
4. Set your GameMode: use **`GM_TetherMode`** directly or make it the parent of your own GameMode.
5. Add the **`BPI_TetherPlayer`** interface to your character.
6. Use the interface functions for network values: **`GetNetworkLocation`**, **`GetNetworkVelocity`**, **`GetNetworkRotation`** rather than direct `GetActorLocation/Rotation/Velocity`.

---

## Client-Authoritative Movement (What it does)
The movement system calculates motion locally on each client and sends authoritative position packets to the server. The server validates and replicates those positions to other clients while applying **LERP smoothing** to reconcile small differences. This yields:

- Immediate, highly responsive input feel  
- Reduced jitter and rubberbanding (smoother corrections)  
- Better perceived movement under high-ping conditions

**Warning:** Because the client computes movement, this model is **vulnerable to client-side cheating** (speed hacks, teleporting, etc.). **Do not use** this system for competitive PvP or ranked modes unless you implement strong anti-cheat measures. For those cases prefer server-authoritative movement.

---

## Player Tether System (What it does)
The tether system connects players as if by a rope. When linked:

- Players influence each other’s movement and are pulled together if they exceed the configured distance  
- The rope simulates physics: **elasticity**, **stretch**, and **tension** can be tuned  
- Multiple players can be linked for group mechanics (e.g., party puzzles, co-op constraints)

Configurable parameters include: **maximum distance**, **elasticity**, **pull strength**, and **group linking** behavior.

---

## Setup and Usage (Detailed)
### Make `BP_MyTetherCharacter` your parent
- Open your character blueprint → **Class Settings** → **Parent Class** → choose `BP_MyTetherCharacter`.  
  This copies the plugin’s base functionality into your character without manual wiring.
  <img src="https://i.hizliresim.com/29h9qv3.png" width="40%" /> <img src="https://i.hizliresim.com/bgab2zg.png" width="40%" />

### Movement replication only
- Add **Movement Replication Component** to your character (Components → Add Component).  
- In the character defaults, **uncheck** `Replicate Movement`.  
- Ensure character has `bReplicates = true` and replication is properly enabled on relevant components.
<img src="https://i.hizliresim.com/hrn5f9p.png" width="40%" /> <img src="https://i.hizliresim.com/ew6jc9h.png" width="40%" />

### GameMode
- Open your project GameMode → set its parent to **`GM_TetherMode`** or assign `GM_TetherMode` as the active GameMode, depending on whether you want to extend it or use it directly.

### Interfaces
- Add **BPI_TetherPlayer** to your character blueprint (Class Settings → Implemented Interfaces).  
- Prefer the interface getters (`GetNetworkLocation`, `GetNetworkVelocity`, `GetNetworkRotation`) in code/blueprints to reduce desyncs and improve network stability.

---

## Example: Animation Blueprint Integration
1. Add **BPI_AnimationBlueprint** to your Animation Blueprint (Class Settings → Implemented Interfaces).  
2. Override the interface functions that appear and map them to animation variables (speed, direction, stance, etc.).  
3. Use the provided network getters to feed animation params instead of direct actor queries.

> Visual examples):  
> <img src="https://i.hizliresim.com/hw6md83.png" width="30%" /> <img src="https://i.hizliresim.com/rjr464f.png" width="30%" /> <img src="https://i.hizliresim.com/9opxl9z.png" width="30%" />

---

## Important Notes & Tips
- **Security:** Client authoritative movement is great for co-op and party games. Avoid it for competitive PvP unless you add server checks or anti-cheat.  
- **Replication flags:** Make sure `bReplicates` is true on characters and relevant components, but `Replicate Movement` is turned OFF when using the plugin movement component.  
- **Performance:** Server LERP reduces jitter but tune interpolation rates for your tick rate to avoid visible smoothing lag.  
- **Multiple tethers:** When linking more than 2 players, test group dynamics carefully (pull strengths and elasticity interact nonlinearly).  
- **Debugging:** Use log prints and the network profiler to inspect replication frequency and packet size if you observe issues.

---

## Troubleshooting
- **Players teleporting/snapping:** Check interpolation settings on the server smoothing and ensure clients are sending consistent, validated movement payloads.  
- **Animation desyncs:** Use the network getters from `BPI_TetherPlayer` inside the Animation Blueprint.  
- **Tether jitter:** Reduce anchor update frequency or increase elasticity damping.

---

## Files & Where to Look
- `BP_MyTetherCharacter` base character (parent blueprint)  
- `MovementReplicationComponent` attach to character to enable movement system only  
- `GM_TetherMode` GameMode with required setup  
- `BPI_TetherPlayer` and `BPI_AnimationBlueprint` interfaces for character & animation integration

---

## Quick Checklist
```text
- [ ] Parent class set to BP_MyTetherCharacter
- [ ] MovementReplicationComponent added (if using movement only)
- [ ] Character: Replicate Movement = OFF
- [ ] GameMode set to GM_TetherMode (or use as parent)
- [ ] Implement BPI_TetherPlayer on character
- [ ] Implement BPI_AnimationBlueprint on AnimBP
