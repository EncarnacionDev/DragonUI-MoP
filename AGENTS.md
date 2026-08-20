# Agent Notes: DragonUI MoP

World of Warcraft **5.4.8 (Mists of Pandaria)** UI addon — Interface version `50400`. Forked from the 3.3.5a DragonUI codebase. Pure Lua, no build system, no package manager, no CI.

This fork replaces 3.3.5a support; it is **not** backwards-compatible with WotLK.

## Project Layout

- **`DragonUI/`** — Main addon. Always loaded.
- **`DragonUI_Options/`** — Load-on-demand configuration UI. Depends on `DragonUI`.
- **`LICENSES/`** — Bundled dependency licenses. Project-authored code is MIT; bundled libs retain their own licenses.

## How to Verify

There is no local test runner. Validation happens inside the game client:

1. Copy both `DragonUI` and `DragonUI_Options` folders into `World of Warcraft/Interface/AddOns/`.
2. Launch 5.4.8 and enable both addons in the character screen AddOns list.
3. In-game: `/dui` opens the config panel, `/dragonui edit` toggles editor mode, `/rl` reloads the UI.
4. Use `/dragonui status` to inspect registered modules and `/dragonui debug on` for diagnostic logging.

To reset all settings: delete `WTF/Account/<Account>/SavedVariables/DragonUIMoPDB*`, or use `/dragonui reset`.

## Key Migration Decisions

- **SavedVariable**: renamed from `DragonUIDB` to `DragonUIMoPDB` to avoid clashing with WotLK profiles.
- **Healing/absorb libraries removed from TOC**: `LibHealComm-4.0` and `AbsorbsMonitor-1.0` are WotLK-only and currently disabled. Incoming-heal/absorb overlays are stubbed out until MoP replacements are found.
- **Vehicle bar disabled**: `modules/actionbars/vehicle.lua` is commented out of `actionbars/actionbars.xml`. MoP uses `OverrideActionBar` instead of `VehicleMenuBar`.
- **Multicast/totem bar disabled**: `modules/actionbars/multicast.lua` is commented out. The shaman totem bar does not exist in MoP.
- **Group APIs**: replaced `GetNumPartyMembers` / `GetNumRaidMembers` with `IsInGroup` / `IsInRaid` / `GetNumGroupMembers` via helpers in `core/api.lua`.
- **Resize APIs**: `SetResizeBounds` does **not** exist in MoP 5.4.8 — code keeps `SetMinResize` / `SetMaxResize` (see `MIGRATION_PLAN.md`, which supersedes an earlier, wrong claim).
- **Casting APIs**: `UnitCastingInfo` / `UnitChannelInfo` unpacks follow the WotLK-format (9/8 returns incl. `nameSubtext`), because this private server returns that, **not** standard MoP's 8/7-value order.
- **Power events**: MoP uses `UNIT_POWER` / `UNIT_MAXPOWER` (carrying a `powerType` token); per-school events (`UNIT_MANA`, `UNIT_RAGE`, …) are kept as fallback because private servers may still fire them. `UNIT_HAPPINESS` was removed.

**`MIGRATION_PLAN.md`** (Spanish) is the running log of every WotLK→MoP fix. Check it first when a module misbehaves — it records private-server quirks (WotLK-format cast returns, `WatchFrame` still used, hex GUIDs in CLEU, etc.).

## Load Order

Same structure as the original addon. The executable source of truth is:

- `DragonUI/DragonUI.toc`
- `DragonUI/DragonUI.xml`
- `DragonUI/core/core.xml`
- `DragonUI/modules/modules.xml`
- `DragonUI_Options/DragonUI_Options.toc`

If you add a file, you **must** register it in the relevant `.toc` or `.xml`.

## Core Architecture

- `_G.DragonUI` is the addon table exposed in `DragonUI/core.lua`.
- `addon.core` is the AceAddon-3.0 object.
- `addon.db` is an AceDB-3.0 profile DB backed by `DragonUIMoPDB` SavedVariables.
- `addon.config` is a metatable proxy that routes reads to `addon.db.profile` and static assets.
- `addon.ModuleRegistry` (`core/api.lua`) is the canonical module system.
- `addon.DB_SCHEMA_VERSION` + `addon:ApplyDatabaseMigrations()` (both in `core/api.lua`) gate profile upgrades — bump the version and add a migration when changing defaults/schema.

## Common Commands

| Command | Purpose |
|---------|---------|
| `/dui` or `/dragonui` | Open settings |
| `/dragonui edit` | Toggle editor/move mode |
| `/dragonui reset` | Reset all positions |
| `/dragonui status` | Module registry status |
| `/dragonui debug on` | Enable diagnostic logs |
| `/dragonui kb` | Toggle keybind mode |
| `/rl` | Reload UI |

Extra user commands (registered elsewhere): `/sort` (bag sort), `/tt <msg>` (whisper target), `/duicomp` (compat diagnostics). Many debug subcommands (`debugvehicle`, `ufl`, `npclamp`, `debugshadow`, `shadowcolor`, `shadowcrop`, `shadowtest`) exist in `core/commands.lua` and require `/dragonui debug on` first.

## Known Disabled / Broken Areas

- Vehicle UI (`OverrideActionBar` not yet implemented).
- Shaman multicast/totem bar.
- Incoming heal and absorb overlays (libraries removed).
- Nameplates may need further layout tweaks for the 5.4.8 native plate structure.
