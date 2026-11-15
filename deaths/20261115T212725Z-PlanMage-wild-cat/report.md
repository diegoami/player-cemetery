# PlanMage: wild-cat death, verified recording

A level-5 Gnome Mage died on DL1 at game turn 411674 with -3/26 HP.
This is a watched, plan-controlled diagnostic game, not a measured Zoo arm.
The session label/save basename is PlanMage; the original character display
name PlanMageCheck was retained by the native save. The fresh-birth capture
and resumed segment are one continued life; replay is not another sample.

## What happened

| Game turn | HP / SP | Chosen check | Action and result |
| --- | --- | --- | --- |
| 411618–411628 | 26/26; 2/10 | borg_think_stair_scum | Steps west from the arrival stair toward nearby copper while the loop is returning to town. |
| 411638 | 26/26; 2/10 | errand_steer | Routes east toward the up stair through a visible wild cat. The movement helper emits +6: a melee attack, which misses. HP falls to 19. |
| 411648 | 19/26; 2/10 | borg_think_stair_scum | Walks west to collect 120 gold of copper, spending an action before caution. HP falls to 14. |
| 411658 | 14/26; 2/10 | errand_steer | Again routes east into a cat instead of making progress to the stair; misses. Four claw messages follow and HP falls to 4. |
| 411668 | 4/26; 2/10 | borg_attack | Chooses Rapier melee, misses, and dies at turn 411674. |

The recorder shows two visible wild cats by frame26816; both have actual
speed118, versus the race's nominal speed120. The engine defines two 1d3
claws, 9 nominal HP, and BASH_DOOR (monster.txt:1133–1148). A native depth2
monster appeared on DL1; native depth is not a hard minimum. The opening
screen shows the up stair beyond the cat and copper behind the Mage.

At the final decision, known gear still contained **16 Cure Light Wounds
and one Cure Serious Wounds potion**, four First Spells books, and no Phase
Door scroll. Phase Door was learned in the fresh-birth segment at turn8081;
2 SP remained, matching its base mana cost. Its precise rejection reason
is not exposed by the selected-check trace; do not assert that a cast would
certainly succeed or that a counterfactual escape has been demonstrated.

## Why the decisions failed

1. **Return-to-town opportunistic loot runs before survival caution.**
   borg_think_dungeon.rs:634–644 calls borg_think_stair_scum before the
   normal caution call at747–750. The helper tries an old object flow and
   nearby loot before leaving (borg_think_dungeon_util.rs:549–558). This
   exactly matches the selected trace at411648 while a cat was attacking.
2. **A retreat flow is permitted to become a bump attack.** The errand's
   stairs_up delegates to GOAL_FLEE flows (shell.rs:1454–1460). The common
   movement helper handles an occupied destination as a melee attack
   (borg_flow.rs:1084–1129). This is what +6 and both 'Walking into a wild
   cat' messages demonstrate: a retreat label did not imply safe movement.
3. **The healing gates leave a gap.** At14/26 HP, loss is46%; danger13 is
   below14, so the fleeing branch does consider healing. With no unique,
   the non-priest chance penalty25 makes the RNG range -25..74, below the
   band3 skip threshold80, so this wound band always refuses healing when
   no status exception applies. The snapshot has neither cut nor poison at
   that point. At4/26, the fleeing branch requires danger < current HP;
   13 < 4 is false and the ordinary potion-healing call is skipped entirely
   (borg_caution.rs:1696–1705). The later reserve branch considers Healing,
   *Healing* and Life, not the CLW/CSW actually carried. Emergency healing
   is therefore not restored by that fallback.

References refer to frozen implementation 820140e0409b34f0ee53cabfbac2b57a39f62e7e.
The strategy manual's FIGHT-02 / HEAL-01 and escape chapter explain why a
fast pursuer needs a next-action survival check rather than just a stair
path. Engine data, not an assumed formula from the guide, grounds the cat.

## What this says about shopping and the plan

M13 shopping was enabled (borg_town_plan=1), trip policy was off, and the
mage plan had advanced to loop-levels0..2. Recorded purchases demonstrate
actual town-plan transactions; it supplied healing that remained unused.
This single death does not validate the whole shopping policy, and there
is no matched shopping-off or plan-off counterfactual. The combat failure
is specifically supported by the chosen checks and recorded actions.
The long town/DL1 bouncing with fleeing_to_town set also merits a separate
loop-progress investigation; it is not needed to establish the lethal
four-action sequence.

## Reproduction and evidence

The completed resumed GUI recording was replayed with the **original frozen
executable**, the preserved starting save, and an isolated profile. The
recorder verified inputs, file bytes, messages, frames and completion receipt.
Final native saves, complete core traces and parameter snapshots are byte
identical. See replay.json. Source and binary identities and launch settings
are retained in the session/export metadata. Actual monster HP/speed and
hidden gear identifiers are diagnostic observations, never additional Borg
inputs.

Both complete original captures, starting/final saves, profiles, plan bytes
and diagnostics are kept in player-cemetery/recordings/20261115T212321Z-PlanMage/.
The frozen binaries stay local in that directory's runtime/. Export archives
exclude executables and duplicated replay output. No strategy fix or claimed
survival counterfactual is included in this analysis.

Tracked fixes: https://github.com/diegoami/borg-reborn-rs/issues/350.

The first virtual-display setup attempt failed before a game could start
because WSLg owns the Unix socket directory. The successful replay used
the normal X11 display with a separate profile; failed setup logs remain
in the local session evidence.
