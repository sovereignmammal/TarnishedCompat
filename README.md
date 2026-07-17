# TarnishedCompat

STS2 compatibility patcher with auto-updates.

Friends: download the latest `TarnishedCompat-patcher.zip` from [Releases](https://github.com/sovereignmammal/TarnishedCompat/releases), extract, close the game, then run `PatchAll.bat`. Later updates: `UpdatePatcher.bat`.

---

## Quick overview of fixes

TarnishedCompat keeps a curated set of Workshop mods playable on current STS2 / public-beta by rewriting stale DLL calls, hardening a few game-side edge cases, and applying light English localization where needed.

### Game (`sts2.dll`)
- **Tezcatara’s Ember** — enchanted cards (including modded ones) correctly become free.
- **MegaLabel theme-font assert** — card library / compendium no longer hard-crashes on missing theme fonts.
- **Rest-site act fallback** — unknown act IDs no longer throw when showing rest-site character animations.

### Mod combat / API retargets
Keeps older mods working after STS2 CardPlay / Damage / Hook signature changes:
- EldenRing (Tarnished2)
- Just Enough Potion
- Boss Mechanics Plus
- Wanderer
- Cultist Simulator Relic
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
- **Sephiroth** — restores character select icon path; disables the global rest/merchant hide that blanked every character (including Classic Acts).
- **Balls2** — DragonBall potion crash + combat-count guard; skips multiplayer-unsafe card-select UIs on DragonBall / Mercury / Mars / CrystalBall / BouncyBall / Marble.
- **Cultist Simulator Relic** — Radiant Substance now applies **Radiance** (energy refund on play) instead of Illumination (draw trigger).
- **ZSMod-Reaper** — Reanimate icons register the missing MegaLabel theme font.
- **Wylder** — SoulTree act map shrunk so RestSite unlocks the boss path.
- **The Unknown** — stubs obsolete `WithCustomPool` so Codex pool calls don’t hang.

### Localization / PCK polish
English display names / character-select text for Cultist, Reimu, WeaponMaster (GrandMaster at Arms), Yuki, LittleWizard, Wanderer, Tarnished, Saber, and Wylder Act 4 labels. Optional Wylder background replacement asset included.

---

## Reasoning

STS2’s mid-EA API churn (especially `CreatureCmd.Damage`, `CardSelectCmd.FromCard`, Hook damage modifiers, potion factory return types, and Spine animation void returns) left many otherwise-good Workshop mods calling methods that no longer exist or have different signatures. Those failures show up as silent no-ops, MissingMethod crashes, InvalidProgramException, softlocks on card select, or blank UI.

TarnishedCompat does **not** replace the mods. It:
1. Backs up originals where needed.
2. Retargets call sites / Harmony signatures onto the current game APIs.
3. Applies a few narrowly scoped behavior guards where the upstream mod path is known-broken in co-op or on unknown acts.
4. Publishes a self-updating zip so users stay on the same patched set.

When a fix changes gameplay (example: Balls2 multiplayer card-select skips, Sephiroth rest/merchant unhide, Radiant → Radiance), it is intentional compatibility triage: prefer a working co-op/singleplayer experience over preserving a broken call path.

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

**Dependencies (unpatched, used by several mods above):**
- BaseLib — Alchyr
- RitsuLib — OLC

**Base game:** MegaCrit / Slay the Spire 2.

English localization strings, Radiant→Radiance retarget, Sephiroth hide neuter, Balls2 MP skips, and similar behavior patches are TarnishedCompat maintenance work and are not claimed as original mod content.
