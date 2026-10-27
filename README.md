# Pup Pop

A musical match-three game in Unreal Engine 5.7 with six 3D toy puppies: corgi, beagle, poodle, husky, pug and dalmatian. It is a standalone project; nothing here depends on the EVA project around it.

The board is a wooden toy stand set in a 3D park, seen through a tilted camera with a gentle tilt-shift blur. Each of the four zones has its own time of day: morning in Clover Meadow, golden hour in Blossom Gardens, sunset at Sunset Shores, and a lamp-lit night in Starlight Park. The puppies are sculpted vinyl-toy models with glossy eyes. They blink, tilt their heads toward what you touch, make happy ^^ faces after a good match, look surprised when something goes off next to them, and doze off with little Zs if you leave them alone.

![Clover Meadow](Docs/Meadow.png)

## Play

Double-click **`Dist/PupPop-v2.0.0.exe`**. It is the whole game in one file that you can copy to any Windows 10 or 11 PC. The file is stored with Git LFS, so clone with Git LFS installed (or run `git lfs pull`) to get the real exe instead of a small pointer file. The first launch unpacks it to `%LOCALAPPDATA%\PupPop\App` in a few seconds; later launches start straight away. Saves go to `%LOCALAPPDATA%\PupPop\Saved`.

To test it hands-free, run `Dist\PupPop-v2.0.0.exe --selftest`. A bot plays seven levels while you watch. After every move it checks that the screen matches the rules, and it ends on a PASS or FAIL card. The result is also written to `%LOCALAPPDATA%\PupPop\Saved\SelfTest.txt` and screenshots to `Saved\Verify` next to it. Add `-PupQuick` for three levels, or `--wait` to have the command return the game's exit code: 0 for PASS, 1 for FAIL.

The development build is in `Builds/Windows` (**Play Pup Pop.cmd** in the parent folder starts it). Keep that whole folder together when copying it.

Pick a park on the map, read its goals, then swap neighbouring pups (click-click or drag) to line up three or more of the same breed.

| Control | Action |
| --- | --- |
| Left click / drag | Select and swap neighbouring pups |
| Arrows, then Space | Move the cursor, pick a pup; arrows then swap it |
| H | Hint |
| W | Pack Howl (when the meter is full) |
| Esc | Pause and settings |
| Enter | Play, continue, next park |
| R | Restart the park |
| M | Music on or off |

## Candy Crush basics

| Match | Makes | When it goes off |
| --- | --- | --- |
| 4 in a line | **Zoomies Pup** (bandana, goggles) | Dashes along the row or column you swiped |
| L or T shape | **Bubble Bath Pup** (rubber duck) | Splashes a 3x3 area, then splashes again after things fall |
| 5 in a line | **Squeaky Ball** | Swap it with any pup to pop that whole breed |
| 2x2 square | **Fetch Pup** (tennis ball) | Runs off to the trickiest spot: kennels, hedges, mud, pups under bones |

Swapping two specials together combos them: Zoomies + Zoomies clears a cross, Zoomies + Bubble a three-wide cross, Bubble + Bubble a 5x5 area, Fetch carries the other special to its target, Ball + any special turns that whole breed into the special, and two Balls clear the park.

Levels have limited moves, 1-3 stars, and goals: score, pop a number of a breed, wash away mud (1 or 2 layers), trim hedges (1 or 2 layers), free pups locked in kennels, and bring bones down to the doghouses at the bottom. Boards can have holes, and pups slide diagonally around blockers. Stuck boards shuffle themselves. When the goals are done, **Pawty Time** turns every leftover move into a Zoomies pup and sets off every special on the board.

## What is new for the puppies

- **The board is an instrument.** The music is a 120 BPM loop in C major. Every pop plays a note on the chord that is playing, cascades climb that chord, and the breeds you matched sing along in their own voices (a corgi yip, a beagle's aroo, a husky's awoo...). Picking up a pup makes it sing its note.
- **Swap on the beat.** The pups bounce, the tails wag and four paws on the right light up with the beat. A swap on the beat scores 50% more.
- **Pack Howl.** Every pop fills a meter. When it glows, press W: the mascot howls, a ring of sound rolls across the park, and four pups become specials without costing a move.
- **Adaptive music.** Four stems (bed, groove, lead, sparkle) mix live: the groove and sparkle rise with combos, the band gets tense on the last five moves, Pawty Time brings everything in, and pausing muffles it.
- **Sound you can see.** Music notes float up from matches, zoomies leave glowing paw prints, the howl and the beat pulse as rings of light.
- Each zone has its own light, backdrop and ambience: Clover Meadow, Blossom Gardens, Sunset Shores, Starlight Park.
- Accessibility: optional breed symbols on the collar tags, screen shake can be turned off, the whole game plays by keyboard.

Progress, stars, best scores and settings are saved locally (`Saved/SaveGames`).

## Look and credits

- Models: every puppy, accessory, board piece and park prop is built by script in Blender (`Scripts/models`) from simple shapes fused into one smooth, vertex-painted mesh. There are no textures except the glyph atlas.
- Effects draw glyphs (sparkles, paws, notes, hearts, bubbles, leaves, petals, Zs) from an atlas drawn by `Scripts/make_glyphs.py`.
- Font: [Fredoka](https://github.com/google/fonts/tree/main/ofl/fredoka) by The Fredoka Project Authors, under the SIL Open Font License 1.1 (`Content/Legal/Fredoka-OFL.txt`, shipped next to the game). The licence allows bundling it with a game you sell; it only forbids selling the font on its own.
- Music, voices and sound effects are synthesized by `Scripts/generate_audio.py`. Everything else is original to this project.
- Licence: the project is under the MIT licence in `LICENSE`, except the Fredoka font, which stays under its own SIL Open Font License.

## Project layout

```
Source/PupPop/Rules   Match-three rules in plain C++ with no Unreal dependency, plus the level file reader and self test
Source/PupPop/Game    Board view, puppies, effects, camera maths (PupStage), adaptive audio, game flow, input, save game
Source/PupPop/UI      Heads-up display, map, level cards and menus
Content/Data          Levels.json: the 20 levels (edit it to make your own; it is also copied next to the packaged game)
Scripts               Build, verification, rules harness, models (Blender), glyphs, sound design and content scripts
Tools/RulesHarness    Standalone runner for the rules tests and the bot playtest
Tools/Launcher        Single-file launcher: unpacks the appended game on first run, then starts it
```

The rules report everything they do as steps of events (swap, clear, upgrade, activate, tile, move, spawn, deliver). The board view only animates those events, and the rules tests replay the same events to prove the view can never lose track of a piece.

## Build and test

- `powershell -ExecutionPolicy Bypass -File Scripts/TestRules.ps1` runs in seconds with no Unreal: the rules self test, a check that every level starts full, settled and playable across 40 seeds, and a sharp and a casual bot playing every level to report win rates and stars.
- `powershell -ExecutionPolicy Bypass -File Scripts/Build.ps1` runs the rules tests, builds the models (Blender 4.4), draws the glyph atlas (Python with Pillow), regenerates the sounds (Python with numpy), compiles, builds the content and packages `Builds/Windows`. Pass `-Engine` or `-Blender` for other install paths, `-SkipPackage` to stop before packaging.
- `powershell -ExecutionPolicy Bypass -File Scripts/MakeApp.ps1` makes the single-file `Dist/PupPop-v<version>.exe` from the Shipping build in `Builds/Shipping/Windows`, packaging that build first if it is missing (`-Package` to always repackage). It compiles the small launcher in `Tools/Launcher` and appends the game to it as a zip (`Scripts/pack_app.py`). The version comes from `ProjectVersion` in `Config/DefaultGame.ini` and is also written into the exe's file details; older versions in `Dist` are removed. A new build unpacks to a new folder on the player's PC and removes the old one.
- `blender -b --factory-startup --python Scripts/models/preview.py` renders the assembled puppies and props to `Saved/Models/Preview*.png` for a quick look without Unreal.
- `powershell -ExecutionPolicy Bypass -File Scripts/Verify.ps1` plays seven levels unattended in the packaged game, checks after every move that what is on screen matches the rules, and saves screenshots to `Builds/Windows/PupPop/Saved/Verify`. Add `-Editor` to run it through the editor instead, `-Quick` to play three levels.

### Level file

Board rows use one character per cell: `.` plain, `x` hole, `m` mud, `M` deep mud, `h` hedge, `H` thick hedge, `k` kennel, `K` kennel on mud, `b` a bone. Goal types are `score` (with `target`), `collect` (with `breed` and `target`), `mud`, `rescue`, `hedges` and `bones` (with `target`). Every cell must be reachable by falling or sliding pups; `Scripts/TestRules.ps1` reports any that are not.
