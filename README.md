# <p align="center">Collectible System UE5</p>
Collectible System is a simple example project to add collectibles in your game<br>

This project is unfinished and can be optimized or extended with additional features. Unlike a hardcoded pick-up counter, it does not store totals inside the Blueprints themselves. Instead, it reads them from a Data Asset, so a new level can be added without editing a single Blueprint.<br>

The system gives every pick-up a unique `CollectibleID` and a `LevelID`, stores collected IDs inside a SaveGame object as a `Map<LevelID, ST_LevelProgress>`, and keeps one array per collectible type. A Game Instance holds the current save, the current level's Data Asset, and two event dispatchers that the interface subscribes to.<br>

For example, it can tell that the player found 1 of 2 Collectible1 and 2 of 2 Collectible2 in `Level_01`, refuse to count the same ID twice, detect two actors sharing an ID at `BeginPlay`, and silently remove a pick-up that was already collected when the level is loaded again.<br>

It also drives the whole interface through event dispatchers instead of polling, allowing the HUD counters, the pick-up notification, and the pause menu summary to stay in sync with the save data without a single `Tick`.<br>

<p align="center">
  <img src="https://github.com/Yhzan95/UE5CollectibleSystem/blob/main/Images/1.png?raw=true" width="49%">
  <img src="https://github.com/Yhzan95/UE5CollectibleSystem/blob/main/Images/2.png?raw=true" width="49%">
</p>

<p align="center">
  <img src="https://github.com/Yhzan95/UE5CollectibleSystem/blob/main/Images/BP.png?raw=true" width="49%">
</p>

## Features
### [Gameplay]
- Overlap Pick-Up (Sphere Trigger, No Input Required)
- Unique Collectible ID (Per Actor)
- Two Collectible Types (Extendable Via Enum)
- Duplicate ID Detection (At BeginPlay)
- Already-Collected Actors Removed On Level Load
- Collision Disabled + Actor Destroyed On Pick-Up
- Per-Type Mesh And Material Variants

### [Save & Progression]
- Per-Level Progress (Map Keyed By LevelID)
- Per-Type Collected ID Arrays
- Deduplicated Storage (AddUnique)
- Collect Collectible (Returns WasNewCollectible)
- Is Collectible Collected
- Get Collectible Count (Per Type)
- Get Level Collected Count (All Types)
- Progress Persists Across Level Changes

### [User Interface]
- Live HUD Counters (X / Total Per Type)
- Total Counter (All Types Combined)
- Animated Pick-Up Notification
- Notification Name, Icon And Type
- Pause Menu Progress Summary (Escape)
- HUD Auto-Hidden While Paused
- Event-Driven Refresh (No Tick and No Polling)

### [Level Configuration]
- Level Totals Driven By Data Asset
- Level ID And Display Name
- Soft Map Reference
- Level Thumbnail
- One Config Actor Per Level
- HUD Spawned Automatically On BeginPlay

### [Architecture]
- 100% Blueprint Gameplay Logic
- Game Instance As State Holder
- On Collectible Changed (Event Dispatcher)
- On Collectible Picked Up (Event Dispatcher)
- Registered Collectible IDs (Set)
- Blueprint Interface Declared For The Save API
- Isolated In A Single Content Folder (Migratable)

## Install Dependencies
```bash
Unreal Engine 5.8
Visual Studio 2022 (Game development with C++ workload)
Windows SDK
No additional third-party plugins are required.
```

## Usage
```js
1) Clone the repository
2) Right-click Collectible.uproject > Generate Visual Studio project files
3) Build the solution, then open Collectible.uproject
4) Open Content/ThirdPerson/Lvl_ThirdPerson and press Play
5) Walk into a collectible to pick it up and watch the HUD update
6) Press Escape to open the pause menu and see the level summary
Note: The Game Instance class must stay set to BP_GI_Game in Project Settings, otherwise nothing initialises.
```

## Adding A Collectible
```js
1) Drop BP_Collectible_1 or BP_Collectible_2 into the level
2) Set CollectibleID (must be unique across the level)
3) Set LevelID (must match the LevelID of the level Data Asset)
4) Set CollectibleType, DisplayName and Icon
5) Increment TotalCollectible1 or TotalCollectible2 in DA_Level_01
Note: A duplicated ID is reported by RegisterCollectibleID at BeginPlay.
```

## Adding A Level
```js
1) Create a Data Asset from PDA_LevelInfo
2) Fill in LevelID, DisplayName, TotalCollectible1, TotalCollectible2 and LevelMap
3) Place a BP_LevelCollectibleConfig in the level
4) Point its LevelData at the new Data Asset
5) Use the same LevelID on every collectible of that level
Note: No Blueprint needs to be modified to add a level.
```

## Project Structure
```bash
Content/Collectible/
├── Blueprints/
│   ├── BP_Collectible              Actor - base pick-up
│   ├── BP_Collectible_1 / _2       Type variants (mesh + material)
│   ├── BP_GI_Game                  GameInstance - state + dispatchers
│   ├── SG_SaveGame                 SaveGame - progress data + API
│   ├── BP_LevelCollectibleConfig   Actor - one per level (config + HUD)
│   ├── PDA_LevelInfo               PrimaryDataAsset - level description
│   └── BPI_CollectibleSave         Blueprint Interface (declared)
├── DataAsset/DA_Level_01           Level_01 - 2 + 2 collectibles
├── Enumeration/E_CollectibleType   Collectible1 | Collectible2
├── Structs/ST_LevelProgress        Collected IDs per type
├── Textures/                       M_Collectible1_Red, M_Collectible2_Blue
└── UI/
    ├── WBP_CollectibleHUD          Live counters
    ├── WBP_CollectibleNotification Animated pick-up toast
    └── WBP_PauseMenu               Progress summary
```
