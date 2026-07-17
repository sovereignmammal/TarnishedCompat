# TarnishedCompat

STS2 compatibility patcher with auto-updates.

Friends: download the latest `TarnishedCompat-patcher.zip` from [Releases](https://github.com/sovereignmammal/TarnishedCompat/releases), extract, close the game, then run `PatchAll.bat`. Later updates: `UpdatePatcher.bat`.

**Steam Workshop collection (subscribe here first):** [TarnishedCompat Mod Collection](https://steamcommunity.com/sharedfiles/filedetails/?id=3749444750)

Recommended flow:
1. Subscribe to the Workshop collection above and let Steam finish downloading.
2. Close Slay the Spire 2.
3. Run `PatchAll.bat` (applies patches **and** locks Workshop files so Steam cannot overwrite them).
4. Play. See **Stopping Steam from overwriting patches** below if you want Workshop author updates later.

---

## Quick overview of fixes

TarnishedCompat keeps a curated set of Workshop mods playable on current STS2 / public-beta by rewriting stale DLL calls, hardening a few game-side edge cases, and applying light English localization where needed.

### Game (`sts2.dll`)
- **Tezcatara’s Ember** — enchanted cards (including modded ones) correctly become free.
- **MegaLabel theme-font assert** — card library / compendium no longer hard-crashes on missing theme fonts.
- **Steam branch-support noise** — Workshop mods tagged `public`-only no longer spam unsupported-branch errors/UI on `public-beta`.
- **Rest-site act fallback** — unknown act IDs no longer throw when showing rest-site character animations.

### Mod combat / API retargets
Keeps older mods working after STS2 CardPlay / Damage / Hook signature changes:
- EldenRing (Tarnished2)
- Just Enough Potion
- Boss Mechanics Plus
- Wanderer
- Cultist Simulator Relic
- YukiMod
- The Unknown
- Reimu Hakurei
- Kakarot
- Illaoi
- SGG Community Pack
- Deadcells
- Saber
- Wylder
- Marisa
- BloodMaze

### Targeted behavior fixes
- **Sephiroth** — restores character select icon path; disables the global rest/merchant hide that blanked every character (including Classic Acts); retargets `CreateDupe()` → `CreateDupe(Player)` and `LoseBlock` for STS2 v0.109.
- **UncappedSpire** — removes obsolete `SavedPropertiesTypeCache.InjectTypeIntoCache` calls that hard-crash ModelDb init on STS2 v0.109; also retargets chapter seed-change hash/`Rng` construction to the v0.109 `UInt64` APIs.
- **Boss Mechanics Plus / Illaoi** — removes obsolete `SavedPropertiesTypeCache` registration calls that fail during startup on STS2 v0.109.
- **PengoTarot** — implements the v0.109 `PlayerChoiceContext` requirements and retargets its Wheel of Fortune damage call.
- **No Duplicate Characters** — retargets seed hashing / `Rng` construction to the v0.109 `UInt64` APIs so starting a run no longer hard-crashes.
- **SpireHeart (Heart of the Spire)** — retargets `RunRngSet.get_Seed` from `UInt32` → `UInt64` (with truncate) so `BeforeCombatStart` no longer aborts combat setup with empty hand / 0 energy.
- **The King's Decree** — retargets stale `RunRngSet.get_Seed` / `Rng(UInt32, Int32)` so entering map events no longer black-screens.
- **Workshop seed/Rng sweep** — `patch-all` (and `patch-seed-apis`) scans every live Workshop DLL for stale `RunRngSet.get_Seed` (`UInt32`), `GetDeterministicHashCode` (`Int32`), and `Rng(UInt32, …)` call sites and retargets them to the v0.109 `UInt64` APIs. This also re-fixes mods Steam overwrites after a Workshop update.
- **Balls2** — DragonBall potion crash + combat-count guard; skips multiplayer-unsafe card-select UIs on DragonBall / Mercury / Mars / CrystalBall / BouncyBall / Marble.
- **Cultist Simulator Relic** — restores Radiant Substance's intended **Illumination** enchantment; fixes **Radiance** itself by running its energy refund through the enchanted card's direct `OnPlay` hook; retargets `CreateDupe()` → `CreateDupe(Player)` for Rebound Sun Book on STS2 v0.109.
- **YukiMod** — retargets `CreateDupe()` → `CreateDupe(Player)` for card replay on STS2 v0.109.
- **Marisa** — Spine `SetAnimation` / `AddAnimation` void-return retarget (same class as Boss Mechanics Plus); overrides `CustomRestSiteAnimPath` so The Ordinary Magician no longer falls back to Ironclad's rest-site skin.
- **ZSMod-Reaper** — Reanimate icons register the missing MegaLabel theme font.
- **Wylder** — SoulTree act map shrunk so RestSite unlocks the boss path; ResultPile retarget uses v0.109 `GetResultLocationForCardPlay`.
- **The Unknown** — stubs obsolete `WithCustomPool` so Codex pool calls don’t hang.
- **Hextech Runes (ARAM Mayhem)** — **Attack-Defense Unity** also matches modded basic Strike/Defend cards that omit Strike/Defend tags (e.g. The Unknown), so obtaining the rune actually pairs them into Iron Waves.

### Localization / PCK polish
English display names / character-select text for Cultist, Reimu, WeaponMaster (GrandMaster at Arms), Yuki, LittleWizard, Wanderer, Tarnished, Saber, and Hextech Runes, plus Wylder Act 4 labels. Hextech Runes' manifest name is translated so its relic attribution and WhatMod label no longer show `海克斯符文`. Wylder's Act 4 boss (**Equilibrious Beast**) gets full English for its name, choice cards, moves, summons, and fight buffs/debuffs — including Hatred tooltips that were hardcoded Chinese in the DLL. Optional Wylder background replacement asset included.

---

## Stopping Steam from overwriting patches

Steam Workshop can re-download mods (especially **SpireHeart**) and silently restore unpatched DLLs. TarnishedCompat blocks that after every `PatchAll` / `sweep-seed`.

**What the lock does**
- Marks live Workshop mod DLLs **read-only**
- Marks `steamapps/workshop/appworkshop_2868840.acf` **read-only** so Steam cannot refresh Workshop state for STS2

**Normal play:** just run `PatchAll.bat` once after subscribe/update — it patches and locks automatically.

**When you want Workshop author updates again**
1. Close Slay the Spire 2.
2. Run `UnprotectWorkshop.bat` (or `TarnishedCompat.exe unprotect-workshop`).
3. Let Steam finish Workshop downloads.
4. Run `PatchAll.bat` again (re-applies patches and re-locks).

Manual helpers in the release zip: `ProtectWorkshop.bat` / `UnprotectWorkshop.bat`.

Steam may show a disk write error while the lock is on; that means the overwrite was blocked. Do not leave Workshop unlocked if you care about seed/API patches staying applied.

### Restoring patches after Steam verify / Workshop updates
If you already unlocked Workshop, verified game files, or otherwise let Steam restore originals:
1. Let Steam finish any downloads.
2. Close Slay the Spire 2.
3. Run `UpdatePatcher.bat` if this is an older TarnishedCompat install.
4. Run `PatchAll.bat` again (re-applies patches and re-locks Workshop files).

Do not run Steam verification after patching unless you intend to rerun the patcher afterward.

---

## Reasoning

STS2’s mid-EA API churn (especially `CreatureCmd.Damage`, `CardSelectCmd.FromCard`, Hook damage modifiers, potion factory return types, Spine animation void returns, and `CardModel.CreateDupe(Player)` on v0.109) left many otherwise-good Workshop mods calling methods that no longer exist or have different signatures. Those failures show up as silent no-ops, MissingMethod crashes, InvalidProgramException, softlocks on card select, or blank UI.

TarnishedCompat does **not** replace the mods. It:
1. Backs up originals where needed.
2. Retargets call sites / Harmony signatures onto the current game APIs.
3. Applies a few narrowly scoped behavior guards where the upstream mod path is known-broken in co-op or on unknown acts (including silencing Workshop `public`-only branch tags on `public-beta`, which only produce UI noise).
4. Publishes a self-updating zip so friends stay on the same patched set.

When a fix changes gameplay (example: Balls2 multiplayer card-select skips or Sephiroth rest/merchant unhide), it is intentional compatibility triage: prefer a working co-op/singleplayer experience over preserving a broken call path. Existing mod behavior is otherwise preserved; Radiant Substance remains Illumination while the separate Radiance enchantment is repaired.

Classic Acts Randomizer is **not** force-patched by default; use Act Toggler / manual `patch-classic-acts` if needed. BaseLib and RitsuLib are resolved as dependencies but not modified.

---

## Credits — original mod authors

All credit for the mods themselves belongs to their authors. TarnishedCompat only ships compatibility patches.

| Mod | Author(s) |
|---|---|
| EldenRing (Tarnished2) | Bruce / Codex |
| Just Enough Potion | Diane |
| Boss Mechanics Plus | Bengal |
| Wanderer | zeqzero |
| Cultist Simulator Relic | Reddo |
| The Unknown | Laughst |
| Reimu Hakurei (`reimumod`) | 贤愚一条、巾凡、猫车、Sam |
| GrandMaster at Arms (WeaponMaster) | Shangzhonglaohua & DeepSeek |
| YukiMod | lozalia, 星晨代谢 |
| LittleWizard | iouter, w_s_L, AOShiRo |
| Kakarot | nzl |
| Illaoi (`俄洛伊`) | Natsuki |
| SGG Community Pack | SipcoGamingGuild |
| Deadcells mod | [CSTG]GongJuYin |
| Saber | Angel |
| Wylder (`追踪者角色mod`) | kashenmir |
| SephirothMod | BrandonS |
| ZSMod-Reaper | zswzxc |
| marisamod | Flynn, Hell, Hohner_257, Kishin, Samsara |
| BloodMaze | GanbaruKing |
| Balls2 | dandylion1740 |
| No Duplicate Characters | gandalf196 |
| Heart of the Spire (SpireHeart) | Boninall |
| Hextech Runes | Natsuki |
| The King's Decree | Bombenstein |
| UncappedSpire | Pr0ject |
| PengoTarot | Pengo |

**Dependencies (unpatched, used by several mods above):**
- BaseLib — Alchyr
- RitsuLib — OLC

**Base game:** MegaCrit / Slay the Spire 2.

English localization strings, Radiant→Radiance retarget, Sephiroth hide neuter, Balls2 MP skips, and similar behavior patches are TarnishedCompat maintenance work and are not claimed as original mod content.
