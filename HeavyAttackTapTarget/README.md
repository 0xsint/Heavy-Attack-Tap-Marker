# Heavy Attack Tap Target

An Elder Scrolls Online addon that marks the enemy you heavy attack and removes the mark again when that enemy dies.
When your heavy attack hits the enemy under your crosshair, the addon puts a target marker on it. A target marker is one of the eight icons the game shows above a unit's head: Square, Star, Orb, Triangle, Moon, Oblivion, Crossed Swords or Skull. Target markers are shared with your group, so everyone can see what to focus. When the marked enemy dies, the addon removes the marker.

> **Why not a real tab target?** The game functions that set and clear the tab target are *private* in the ESO API, so no addon is allowed to call them. A target marker is the closest an addon can get.

## Requirements

- **The Elder Scrolls Online** (PC/Mac). The manifest lists API versions 101048–101050. If your game is on a newer version, tick "Allow out of date addons" in the add-ons list.
- **LibAddonMenu-2.0** (optional): adds a settings panel. Without it, use the `/hatt` slash commands.
- **LibGroupBroadcast** (optional): needed for the caller feature. Everyone in the group who wants to follow the caller needs it, plus this addon.

## Installation

1. Copy the `HeavyAttackTapTarget` folder into your AddOns folder:
   - Live: `Documents\Elder Scrolls Online\live\AddOns\`
   - PTS: `Documents\Elder Scrolls Online\pts\AddOns\`
2. Install LibAddonMenu-2.0 and LibGroupBroadcast the same way if you want the settings panel and caller feature.
3. Start the game, or type `/reloadui` if it is already running.
4. On the character select screen, open **Add-Ons** and make sure **Heavy Attack Tap Target** is enabled.

## How it works

### What gets marked

A heavy attack hit marks its target only when all of these are true:

- The addon is enabled.
- The hit landed on the enemy under your crosshair. Splash damage on other enemies is ignored, because the game only lets an addon mark the unit under the crosshair.
- The target is alive and **hostile**: an enemy monster (red name) or an enemy player. Neutral (yellow) creatures, citizens and critters are skipped, even though you can attack them.
- If **Players only** is on, the target is a player.
- The target has no marker yet. The addon never replaces a marker that someone else placed. If the target already carries *your chosen* marker, the addon simply starts tracking it so it is removed on death.
- You are allowed to mark right now (see below).

Each marker can only be on one unit at a time, so heavy attacking a new enemy moves the marker to it.

### When the marker is removed

When the marked enemy dies, the marker is removed. The game only lets an addon change the marker on whatever is under your crosshair, so if you are looking elsewhere when the enemy dies, the marker is removed the next time your crosshair passes over the body.

### Who is allowed to mark

Because markers are shared with the whole group, the addon decides who may place them. The rules are checked in this order:

| Situation | Who can mark |
|---|---|
| You are not in a group | You, always |
| A caller is set and **Use caller** is on | Only the caller |
| **Group leader only** is off | Anyone |
| In a battleground with **Everyone can mark in battlegrounds** on | Anyone |
| Otherwise | Only the group leader |

These rules only limit *placing* markers. A marker you already placed is still removed when its enemy dies, even if you have since lost the right to mark.

### The caller

Battlegrounds hand the group crown to a random player, so "leader only" often means the wrong person is marking. The caller fixes that: one group member claims the role, and from then on only the caller's heavy attacks place markers, no matter who has the crown.

- Anyone with the addon and LibGroupBroadcast can become the caller. The newest claim wins.
- Every group member's addon prints a chat line when the caller changes.
- When someone joins the group, the caller's addon repeats the claim after two seconds so the newcomer knows who the caller is.
- If the caller leaves the group, the role is cleared and everyone falls back to the leader and battleground rules.
- Turning **Use caller** off while you are the caller gives up the role.

## Settings

Open them from **Settings → Addons → Heavy Attack Tap Target**, or with `/hatt settings` or `/hattsettings`. Settings are account wide, so all your characters share them.

| Setting | Default | What it does |
|---|---|---|
| Enabled | On | Master switch for marking. |
| Marker | 1 – Square | Which of the eight target markers to place. |
| Players only | Off | Only mark enemy players, never monsters or NPCs. |
| Group leader only | On | While grouped, only mark when you are the group leader. Solo, you can always mark. |
| Everyone can mark in battlegrounds | On | Inside a battleground, ignore "Group leader only". Greyed out when "Group leader only" is off. |
| Use caller | On | Follow the group's caller, and allow yourself to become one. Greyed out without LibGroupBroadcast. |
| Become caller / Stop being caller | — | Buttons to claim or give up the caller role. |

## Slash commands

| Command | What it does |
|---|---|
| `/hatt` | Show help with the current value of every setting. |
| `/hatt on` / `/hatt off` | Enable or disable marking. |
| `/hatt marker 1-8` | Choose the marker (1 Square, 2 Star, 3 Orb, 4 Triangle, 5 Moon, 6 Oblivion, 7 Crossed Swords, 8 Skull). |
| `/hatt players on` / `off` | Players only. |
| `/hatt leader on` / `off` | Group leader only. |
| `/hatt bg on` / `off` | Everyone can mark in battlegrounds. |
| `/hatt usecaller on` / `off` | Use caller. |
| `/hatt caller` | Become the caller. |
| `/hatt caller off` | Stop being the caller. |
| `/hatt caller who` | Show who the caller is. |
| `/hatt settings` | Open the settings panel. |

## Known limitations

- **No real tab target.** See the note at the top.
- **Marking happens when the hit lands,** not when you start holding the heavy attack.
- **Shared markers.** If several groupmates use the addon with the same marker and nobody is the caller, the marker jumps to whoever heavy attacked last. Pick different markers, or use a caller.
- **Reloading forgets the caller.** After a `/reloadui`, a group member does not know who the caller is until the caller claims again or someone joins the group. If the caller reloads, they need to claim again.
- **Changing marker mid-fight** stops tracking the old marker, so one already placed stays until you clear it yourself.
- **Neutral creatures may slip through.** If a neutral creature turns hostile the moment your heavy attack hits it, it may still get marked. This has not been checked in game.

## For developers

- All code is in `HeavyAttackTapTarget.lua`, with comments describing each section.
- Saved variables: `HeavyAttackTapTargetSV` (account wide, version 1).
- **Before a public release, reserve a LibGroupBroadcast protocol id** at <https://wiki.esoui.com/LibGroupBroadcast_IDs> and set `CALLER_PROTOCOL_ID` to it. The current value, `717`, is a placeholder that has not been reserved. If it clashes with another installed addon, the caller feature turns itself off with a chat message and the rest of the addon keeps working.

## Author

Vixen Hunny
