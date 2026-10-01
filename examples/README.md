# newdles examples

Seven small scenes in one shared lab, one `.blend` each. Every file has the same room, lights and
two orthographic cameras:

- **CAM Iso**: true isometric, from above.
- **CAM Front**: head-on, tilted down slightly, so the specimens stand side by side against the wall grid.

Open a file and press play from frame 1. Each file has a **README** text (Text Editor) that explains
what to look at and what to try.

| File | What it shows | Measured in the file |
|---|---|---|
| `01_stacking.blend` | Rigid bodies land and stack: a pyramid dropped row by row, a 10-cube pyramid, a four-size podium | tops within 2 mm of exact, drift < 0.5 mm after landing |
| `02_friction.blend` | Eight identical blocks on wedges from 20° to 34°, Friction 0.5 | hold up to 28° (≤ 4 mm), slide from 30°; Coulomb says 26.6° |
| `03_press.blend` | An animated collider presses three rubber blocks to half height | 2.00 mm to the press **and** the floor while squeezed; shape back within 1.1 mm |
| `04_stiffness.blend` | The same cube dropped at stiffness 1, 0.9, 0.75, 0.5, 0.25 and 0.05 | 22.0 → 20.0 → 19.6 → 7.5 → 5.0 → 1.4 cm tall |
| `05_stiffness_map.blend` | Four identical planks on two supports; only the painted map differs | rigid plank sags 0.2 mm; soft-middle plank sags 10.7 cm, its rigid ends 0.2 mm |
| `06_self_collision.blend` | A lined napkin (one body, two facing layers) over a post: Self Collision off vs on | layers apart 24 % vs 82 %, crossed 41 % vs 6 %; on the floor 2.8 mm apart |
| `07_cloth.blend` | A 1.4 m sheet at stiffness 0.1 over a sphere and a block | 3.0 mm above the floor, 2.2–2.6 mm off the sphere, never inside |

All numbers were measured headless in Blender 5.1.1, stepping each file from frame 1.

## How every file is built

- **Specimens** (hidden collection) hold the bodies to simulate: one object is one body.
- **Equipment** holds visible lab objects that the solver also sees. Resting surfaces are passive
  bodies (`active` = False) and moving tools are colliders.
- **RB Simulation** carries the Geometry Nodes modifier. Its group feeds the `newdles` node through
  Collection Info (Separate Children, Relative) and outputs only the simulated specimens.
- **Lanes**: examples that compare soft specimens use one solver node per specimen, in labelled
  frames.

Colours in examples 04 and 05 come from the `stiffness` attribute that the solver writes on its
output: blue is rigid, yellow is soft.

The files render with EEVEE by default. Cycles is set up too, but it can shade simulated layers that
rest only a few millimetres apart (examples 04 and 06) as black patches.