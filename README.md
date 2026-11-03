# ⚔️ MMF — Mike's Milsim Framework (Arma III)

[![Arma 3 Compatible](https://img.shields.io/badge/Arma%203-v2.14%2B-blue.svg?logo=arma&logoColor=white)](https://arma3.com/)
[![Workshop Mod](https://img.shields.io/badge/Steam%20Workshop-Available-green.svg?logo=steam)](https://steamcommunity.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub Issues](https://img.shields.io/github/issues/OfficialMikeJ/mmf-mikesmilsimframework-arma-III)](https://github.com/OfficialMikeJ/mmf-mikesmilsimframework-arma-III/issues)

**MMF (Mike's Milsim Framework)** is a fully customizable, server-authoritative procedural milsim mission generation and campaign system for **Arma III**. Built entirely in native SQF, MMF dynamically generates endless tactical operations, coordinates opposing force defenses, places concealed roadside IED hazards, assigns real-world military lore callsigns, provides overhead recon drones, enforces strict mod-only armories, handles smoke-called helicopter extractions, and tracks campaign progress across server restarts without repeating recently played sectors.

---

> [!NOTE]
> **Repository Purpose:** This GitHub repository serves as the **official issue tracker, feature reference, and mod-compatibility hub** for MMF. The actual compiled mod and mission files are distributed through the **Steam Workshop**. Source files are not tracked in this repository.
> 
> * **Steam Workshop:** Search for `Mike's Milsim Framework (MMF)`
> * **Issue Tracker:** [Report a Bug or Request a Feature](https://github.com/OfficialMikeJ/mmf-mikesmilsimframework-arma-III/issues)
> * **Repository Link:** [OfficialMikeJ/mmf-mikesmilsimframework-arma-III](https://github.com/OfficialMikeJ/mmf-mikesmilsimframework-arma-III.git)

---

## 📋 Table of Contents
- [Key Features](#-key-features)
- [Primary Mission Archetypes](#-primary-mission-archetypes)
- [Supported Mods & Content Integration](#-supported-mods--content-integration)
- [Tactical Inventory & Armory Systems](#-tactical-inventory--armory-systems)
  - [Mandatory Mod-Only Gear Restriction](#mandatory-mod-only-gear-restriction)
  - [ACE3 & Virtual Arsenal Crate Init](#ace3--virtual-arsenal-crate-init)
  - [Predefined Role Loadouts & Dynamic Role Names](#predefined-role-loadouts--dynamic-role-names)
- [Field Support Systems](#-field-support-systems)
  - [Autonomous Overhead Recon Drone](#autonomous-overhead-recon-drone)
  - [NPC Helicopter Exfil via Colored Smoke](#npc-helicopter-exfil-via-colored-smoke)
- [Campaign & Gameplay Systems](#-campaign--gameplay-systems)
  - [Continuous Campaign Loop](#continuous-campaign-loop)
  - [Safe Zone & Exclusion System](#safe-zone--exclusion-system)
  - [Real-World Military Lore Callsigns](#real-world-military-lore-callsigns)
  - [Roadside IED & Route Clearance System](#roadside-ied--route-clearance-system)
  - [Persistent History & Anti-Repetition Engine](#persistent-history--anti-repetition-engine)
- [Configuration Architecture](#-configuration-architecture)
- [Bug Reports & Feature Requests](#-bug-reports--feature-requests)
- [Credits & Acknowledgments](#-credits--acknowledgments)

---

## 🌟 Key Features

* **Server-Authoritative Continuous Operations:** Automatically generates follow-on operations upon main mission completion, immediately retasks the Task Force upon mission failure, and generates fresh scenarios on manual server restart.
* **Strict Mod-Only Gear Restriction Engine:** Hardcoded filter ensures that whenever community mods (RHS, CUP, 3CB, HLC, Tier1, SMA, ACE) are loaded, all vanilla, official DLC, and Creator DLC (CDLC) weapons, gear, uniforms, vests, helmets, and ammunition are purged from AI, player slots, and arsenals.
* **ACE3 Arsenal Crate Initializer:** Drop `[this] call MF_fnc_initArsenal;` into any crate/ammobox init field in Eden Editor to populate a clean, mod-only armory.
* **Predefined Tactical Role Loadouts:** Optional specialized loadouts (e.g. Light Machine Gunner carrying squad medical supplies, extra rifle magazines, extra rockets, and drum ammo) and dynamic role name overrides.
* **Autonomous Overhead Recon Drone:** Deploy high-altitude recon UAV orbiting active operations at 750m AGL. Linked to UAV terminals for seamless manual pilot/gunner override, thermal recon, and missile strikes.
* **NPC Helicopter Exfil via Purple Smoke:** Throwing designated smoke (default `SmokeShellPurple`) calls a dustoff helicopter. AI pilot accurately counts live squad operators in the LZ zone and **strictly holds on the deck until all operators are aboard** before returning to base.
* **Modular SQF Configuration Architecture:** 8 dedicated configuration files with over 60 documented variables. Every system features independent boolean toggles (`true`/`false`) and numeric `0` disable logic.
* **Deterministic Dual-LCG PRNG Seed Engine:** Choose between **Random Seed Mode** (unlimited fresh scenarios) or **Manual Seed Mode** (reproducible seed for training, debugging, or competitive scenarios).
* **Cross-Restart Persistent Mission History:** Bounded history stored in server `profileNamespace` using multi-factor similarity scoring (AO distance, mission type, sub-objectives, faction, modifiers) to ensure recently played sectors are not repeated.

---

## 🎯 Primary Mission Archetypes

MMF includes 7 tactical primary mission archetypes that rotate dynamically based on the active terrain:

| Mission Archetype | Objective Description | Target Assets |
| :--- | :--- | :--- |
| **`DESTROY_COMMS`** | Destroy Enemy Comms Relays | Fortified relay nodes, antennas, and encrypted tactical communications caches. |
| **`CAPTURE_COMMANDER`** | Neutralize Enemy Commander / HVT | High-value enemy officer guarded by dedicated bodyguard details in fortified structures. |
| **`CLEAR_STRONGHOLD`** | Seize Enemy Stronghold | Assault a fortified settlement or base and reduce enemy force strength below 20%. |
| **`SABOTAGE_DEPOT`** | Sabotage Logistics Depot | Demolish forward-staged fuel trucks, ammunition caches, and supply transport vehicles. |
| **`RECOVER_INTEL`** | Secure Command Intelligence | Infiltrate enemy command posts to seize encrypted terminals and battle plans. |
| **`CONVOY_INTERDICTION`** | Interdict Armored Supply Column | Ambush and destroy staged enemy command & armored supply columns before transit. |
| **`EOD_CLEARANCE`** | Route Clearance & Sector Sweep | Sweep main approach routes, detect emplaced roadside IEDs, and perform EOD disarm or blow-in-place operations. |

---

## 📦 Supported Mods & Content Integration

MMF features an intelligent **category-aware classname validation engine** (`MF_fnc_validateClassnames`). When modded classnames are provided in configuration arrays, the framework verifies their inheritance, `scope == 2`, and configuration class. Unloaded or missing mods are safely ignored, allowing the same mission file to adapt seamlessly between vanilla and heavily modded servers.

### Officially Validated & Supported Mod Ecosystems

<details>
<summary><b>1. Vanilla Arma III & Official DLCs</b> (Click to expand)</summary>

* **Base Game:** NATO, CSAT, AAF, FIA, Civilians
* **Apex Expansion:** Pacific NATO, CSAT Pacific, Syndikat, Tanoa Gendarmerie
* **Contact DLC:** LDF (Livonian Defense Force), Spetsnaz, Russian Contact assets
* **Creator DLCs:** Fully compatible with Western Sahara, S.O.G. Prairie Fire, CSLA Iron Curtain, and Global Mobilization by adding their unit and vehicle classnames to `config\cfg_object_pools.sqf`.
</details>

<details>
<summary><b>2. RHS — Red Hammer Studios</b> (Click to expand)</summary>

* **RHS: AFRF (Armed Forces of the Russian Federation):**
  * Infantry: VDV, MSV, Naval Infantry, Spetsnaz GRU
  * Ground Vehicles: BTR-70, BTR-80, BTR-82A, BMP-2, BMP-3, T-72B3, T-80, T-90A, Tigr MRAP, KamAZ transport trucks
  * Rotary-Wing: Mi-8MT, Mi-24V/P Hind, Ka-52 Alligator
  * Static Defenses: Kornet ATGM, Igla-S AA, DShKM, AGS-30
* **RHS: USAF (United States Armed Forces):**
  * Infantry: US Army (OEF-CP / UCP), USMC (MARPAT Woodland / Desert)
  * Ground Vehicles: M1151 / M1165 HMMWV, M1220 / M1230 MaxxPro MRAP, M2A2 / M2A3 Bradley, M1A1 / M1A2 Abrams, FMTV trucks
  * Rotary-Wing: UH-60M Blackhawk, CH-47F Chinook, AH-64D Apache, AH-1Z Viper
  * Static Defenses: M2 .50 cal (low/high), Mk19 GMG, TOW missile launcher
* **RHS: GREF & SAF (Greens & Serbian Armed Forces):**
  * Insurgent, Militia, and regional national army infantry templates and technical vehicles.
</details>

<details>
<summary><b>3. CUP — Community Upgrade Project</b> (Click to expand)</summary>

* **CUP Units & Weapons:** Full compatibility with Takistani Army, Chernarussian Movement of the Red Star (ChDKZ), Takistani Militia, US Army, and Russian Federation infantry pools.
* **CUP Vehicles:** BTR-60/90, T-55, T-72, BMP-1/2, Land Rovers, Hilux technicals, UAZ, Ural transport trucks, and armed light patrol vehicles.
</details>

<details>
<summary><b>4. ACE3 — Advanced Combat Environment</b> (Click to expand)</summary>

* **ACE Arsenal:** Clean native armory whitelist populated strictly with loaded mod weapons and equipment.
* **EOD & Explosives Interaction Menu:** Dismounted players can inspect and disarm roadside IEDs via ACE3 radial actions.
* **Specialist Equipment Recognition:**
  * Detectors: `ACE_VMH3`, `ACE_VMM3`, vanilla `MineDetector`
  * EOD Tools: `ACE_DefusalKit`, vanilla `ToolKit`
  * Traits: `explosiveSpecialist`, `engineer`
* **Medical Compatibility:** Unconscious state checks (`ACE_isUnconscious`) integrated into Task Force wipe failure logic.
</details>

<details>
<summary><b>5. 3CB Factions & 3CB BAF</b> (Click to expand)</summary>

* Compatible with 3CB British Armed Forces equipment and vehicles.
* Supports 3CB Middle Eastern, Cold War, and Eastern European insurgent and governmental factions.
</details>

---

## 🎒 Tactical Inventory & Armory Systems

### Mandatory Mod-Only Gear Restriction
To maintain milsim realism and clean arsenals, MMF includes a hardcoded restriction engine (`MF_fnc_filterModContent` & `MF_fnc_enforceModGear`):
* **Purges Vanilla Gear:** When community mods are loaded, all vanilla, DLC, and CDLC weapons, gear, uniforms, vests, helmets, and ammunition are completely barred.
* **AI Gear Replacement:** Enemy AI units spawned in patrols, garrisons, and fortifications are automatically stripped of vanilla weapons and equipped with modded armaments.
* **Playable Slot Protection:** Newly added player or civilian slots are prevented from spawning with pre-made vanilla gear. An inventory event guard prevents players from taking vanilla weapons.
* **Vehicles Unaffected:** Mission makers can still freely place vanilla or modded vehicles; restrictions apply exclusively to personal weapons, gear, and ammunition.
* **Integrity Enforcement:** The restriction check is hardcoded into the framework core and will trigger a critical security abort if files are removed or modified. If **no** community mods are loaded, vanilla gear is cleanly permitted as a fallback.

### ACE3 & Virtual Arsenal Crate Init
Mission makers can turn any placed crate, ammobox, or vehicle into a modded armory with a single Eden Editor init line:

```sqf
[this] call MF_fnc_initArsenal;
```

* Clears default container clutter.
* Automatically whitelists every detected community mod item in ACE Arsenal (or BIS Virtual Arsenal if ACE3 is absent).
* Adds a direct scroll-wheel interaction to access the armory.

### Predefined Role Loadouts & Dynamic Role Names
Configured in `config\cfg_player_loadouts.sqf`:
* **`MF_EnableCustomRoleLoadouts`** (Disabled by default): When set to `true`, automatically assigns custom kits based on player role.
  * *Light Machine Gunner (LMG):* Carries squad medical supplies, extra magazines for riflemen, extra rockets for the AT specialist, and high-capacity drum/box ammunition.
  * *Combat Medic:* Carries field dressings, elastic bandages, morphine, epinephrine, blood IVs, splints, and smoke grenades.
  * *Anti-Tank (LAT):* Carries rocket launchers, AT warheads, and smoke grenades.
  * *Designated Marksman (DMR):* Precision optics and rangefinder.
* **`MF_EnableCustomRoleNames`** (Disabled by default): When set to `true`, dynamically overrides lobby/role selection screen names using the `MF_CustomRoleNameOverrides` array (e.g. "Autorifleman" displays as "Heavy Support Gunner (LMG)").

---

## 🚁 Field Support Systems

Configured in `config\cfg_support_systems.sqf`:

### Autonomous Overhead Recon Drone
* **`MF_EnableReconDrone = true;`**: Automatically deploys a friendly high-altitude UAV (default: MQ-4A Greyhawk / `B_UAV_02_dynamicLoadout_F`) orbiting 750m AGL above the active Area of Operations.
* **UAV Terminal Linking:** Any friendly player with a UAV Terminal can connect, take manual pilot or gunner control, steer, adjust altitude and airspeed, utilize FLIR/thermal optics, and fire precision missiles.
* **Autonomous Orbit Retention:** When the operator releases control, the UAV immediately resumes its autonomous orbit.
* **Campaign Re-Tasking:** Automatically re-tasks its loiter orbit when the campaign transitions to a new AO.

### NPC Helicopter Exfil via Colored Smoke
* **`MF_EnableExfilSupport = true;`**: Calling extraction is as simple as throwing a designated smoke grenade (default: `SmokeShellPurple`).
* **Headcount Awareness:** When the smoke lands, the framework scans the extraction zone (`MF_ExfilPlayerDetectRadius`, default 400m) to count living friendly operators.
* **Strict Boarding Enforcement:** The AI pilot touches down on the deck and **will NOT take off until every single living operator in the extraction zone has boarded the helicopter**.
* **Base Return:** Once all operators are aboard, the helicopter lifts off, navigates back to base/HQ, lands, and orders disembarkation.

---

## 🕹️ Campaign & Gameplay Systems

### Continuous Campaign Loop
MMF runs an automated server-authoritative campaign lifecycle:
1. **On Mission Pass:** Leftover hostile AI and unoccupied enemy vehicles are cleaned up after `MF_NextMissionDelay` (default: 20s), and a new random mission is generated with fresh briefing orders.
2. **On Mission Failure:** If the mission times out (`MF_MissionTimeLimit`) or the Task Force wipes in the AO (`MF_FailOnAOWipe`), the sector marker turns **Red (`[FAILED]`)**, enemy difficulty escalates, and a brand-new mission is immediately generated.
3. **Manual Server Restarts:** Manual restarts read historical records from `profileNamespace` and generate a fresh sector that avoids repeating recent areas.

### Safe Zone & Exclusion System
To prevent enemy AI from attacking players while rearming, taking off, or traveling, MMF enforces 3 layers of spatial exclusion:
* **Base Safe Zones:** 1000m standoff around friendly spawn markers (`respawn_west`, `respawn_east`, `MF_SafeZone_Base`).
* **Military Airport Exclusions:** 1600m standoff around all primary and secondary runways, ILS locations (`CfgWorlds >> ilsPosition`), and terrain airfield locations.
* **Player Distance Standoff:** 1200m minimum standoff between live players and any newly generated mission AO.

### Real-World Military Lore Callsigns
Every generated operation randomly assigns an authentic historical or modern tactical callsign to the player group via `setGroupIdGlobal`:
* **Historical:** `OVERLORD 1-1` (WWII), `SPEARHEAD 3-6` (WWII/Desert Storm), `DEVIL 6-1` (82nd Abn), `CURRAHEE 2-1` (101st Abn), `MACV-SOG HATACHET 1` (Vietnam), `BLACKWATCH 1-6` (Historic Commonwealth).
* **Modern:** `NIGHTSTALKER 1-1` (160th SOAR), `SABRE 2-2` (22 SAS), `RAIDER 1-4` (MARSOC), `DAGGER 1-2` (1st ID), `IRONHORSE 3-1` (1st Cav), `ARCHANGEL 6-2` (USAF CSAR), `REAPER 1-3`, `WARHORSE 3-4`.

### Roadside IED & Route Clearance System
* **Native Geometry Road Scanning:** Uses `nearRoads` and `getRoadInfo` to compute exact road-edge offsets (`MF_IEDRoadOffsetMin` to `MF_IEDRoadOffset`), avoiding bridges, water, and inaccessible terrain.
* **Speed-Threshold Proximity Detonations:** Moving vehicles and infantry trigger detonation if exceeding `MF_IEDTriggerSpeedThreshold` (7 km/h default), allowing dismounted EOD specialists to slowly approach and disarm.
* **Blow-In-Place (BIP):** High-caliber gunfire or standoff explosives trigger realistic sympathetic detonation.

### Persistent History & Anti-Repetition Engine
* Tracks past missions across server restarts using `profileNamespace` (no external database required).
* Evaluates candidate scenarios against a multi-factor weighted similarity formula:
  $$\text{Similarity} = (w_{\text{type}} \cdot S_{\text{type}}) + (w_{\text{area}} \cdot S_{\text{area}}) + (w_{\text{pos}} \cdot S_{\text{pos}}) + (w_{\text{fac}} \cdot S_{\text{fac}}) + (w_{\text{mods}} \cdot S_{\text{mods}})$$
* Enforces an Area Reuse Cooldown (`MF_AreaReuseCooldown`) and minimum spatial separation (`MF_MissionLocationMinSeparation`) from the last completed mission.

---

## ⚙️ Configuration Architecture

All framework settings are organized across 8 modular native SQF configuration files located in `config\`:

```text
config/
├── mission_config.sqf                  # Core AO radii, player sides, base/airport exclusions & Lore Callsigns
├── cfg_feature_toggles.sqf             # Independent true/false switches for all subsystems
├── cfg_enemy_forces.sqf                # Patrol counts, squad sizes, AI sub-skills, QRF waves & civilians
├── cfg_object_pools.sqf                # Classname arrays for infantry, vehicles, static weapons, props & factions
├── cfg_ied_system.sqf                  # Roadside IED placement geometry, offsets, detection & ACE3 settings
├── cfg_randomization_replayability.sqf # Seed modes, continuous loop delays, side mission bonuses & failure escalation
├── cfg_player_loadouts.sqf             # Predefined tactical role loadouts (LMG, Medic, LAT, DMR) & role name overrides
└── cfg_support_systems.sqf             # Autonomous Recon Drone altitude/orbit & Purple Smoke Helicopter Exfil
```

---

## 🐛 Bug Reports & Feature Requests

Found a bug, have an idea for a new mission archetype, or want support for a specific mod added? Please use the GitHub Issue Tracker!

### When reporting a bug, please include:
1. **Map / Terrain:** (e.g., Altis, Malden, Tanoa, Livonia, Chernarus)
2. **Active Modset:** (e.g., Vanilla, RHS, CUP, ACE3)
3. **Multiplayer Environment:** (Dedicated Server, Hosted MP / Local Host, Singleplayer Preview)
4. **Arma III RPT Log:** Snippet from your `.rpt` log file around the `[MF]` framework tags.

👉 **[Open a New Issue on GitHub](https://github.com/OfficialMikeJ/mmf-mikesmilsimframework-arma-III/issues)**

---

## 📜 Credits & Acknowledgments

* **Author / Project Lead:** [OfficialMikeJ](https://github.com/OfficialMikeJ)
* **Development:** Tactical Game Servers
* **Game Engine:** Bohemia Interactive (Arma III)
* **Special Thanks:** The Arma III milsim community and community mod teams (RHS, CUP, ACE3).

---

<p align="center">
  <b>MMF — Dynamic Tactical Operations for Serious Milsim</b><br/>
  <sub>Distributed via Steam Workshop | Managed & Tracked via GitHub</sub>
</p>
