<p>
  <img src="docs/assets/banner.jpg" width="100%" alt="">
</p>

<h1>newdles</h1>

<h3>Soft and Rigid Bodies for Blender Geometry Nodes</h3>



<p>
  <a href="https://www.blender.org/download/"><img alt="Blender 5.1+" src="https://img.shields.io/badge/Blender-5.1%2B-E87D0D?style=flat-square&logo=blender&logoColor=white&labelColor=1B1F27"></a>
  <a href="newdles.blend"><img alt="Version 1.4.0" src="https://img.shields.io/badge/version-1.4.0-34E0A1?style=flat-square&labelColor=1B1F27"></a>
  <a href="#how-it-works"><img alt="100% Geometry Nodes" src="https://img.shields.io/badge/100%25-Geometry%20Nodes-00D6A3?style=flat-square&labelColor=1B1F27"></a>
</p>


## Why newdles

- **Rigid, soft, cloth and goo, together.** One **Stiffness** dial runs from rigid (1) through rubber (about 0.9),
  soft (0.5) and cloth (0.1) to goo (0.01) and loose particles (0). Every kind of body goes through the same solver,
  so every one of them collides with all the others.
- **Paint where it's soft.** Stiffness is a map as well as a dial. Paint it, weight it with vertex groups, or drive
  it with any texture or field: rigid teeth in a soft face, a stiff spine in a floppy tail.
- **Contacts that rest where they touch.** Every contact comes to rest at **Thickness**, the gap you choose, 2 mm
  by default. Contacts are found at the start of each substep, over a distance that grows with the motion, so a thin
  gap doesn't mean tunnelling. At 4 substeps, fast drops end with zero penetration, and a cube moving 4 m per
  substep still stops on a thin plate.
- **Self collision that knows where the air is.** A soft body collides with its own surface wherever there was air
  at rest: lips on teeth, an eyelid on its eyeball, the walls of a hollow bag. Its own neighbourhood never collides,
  and neither do parts built into each other, like tooth roots in the gums.
- **Any input, any frame.** The input is read fresh every frame: procedural meshes, changing vertex counts, bodies
  that come and go. Every particle keeps its simulated state, and a vertex that exists in both frames keeps its
  position exactly.
- **Rigid stays rigid, stacks stay stacked.** Rigid bodies hold their shape to better than one part in a million
  through impacts. Five boxes stack, each within 0.8 mm of its exact height. Passive bodies follow their animation
  to 3e-8 and still push.
- **Just Geometry Nodes.** No add-on, no Python, no drivers: one node group in your file. It opens, plays, bakes
  and renders wherever Blender 5.1 or newer runs.

## Quick start

**You need** Blender 5.1 or newer, and nothing else. The solver is one node group, built and tested on Blender 5.1.1.

1. **Download** [`newdles.blend`](newdles.blend) from this repository.
2. **Append the solver** into your file: *File › Append…*, open `newdles.blend`, then `NodeTree`, and pick
   **newdles**.
3. **Add it to an object.** Select the object that should fall, add a **Geometry Nodes** modifier and click **New**.
   In the node editor, add the solver with <kbd>Shift</kbd> <kbd>A</kbd> › **Group** › **newdles**, and drop
   it onto the link between Group Input and Group Output.
4. **Turn on gravity.** Set **Forces** to `(0, 0, -9.81)`. It is zero by default, so nothing falls until you do.
5. **Give it a floor.** Add an **Object Info** node for your floor object, set it to **Relative**, and link its
   **Geometry** into **Colliders**.
6. **Press play** from the first frame. The solver simulates as the timeline plays and caches every frame it
   simulates. To render on a farm, or frames out of order, bake it first, like any simulation.

The solver works in the local space of the object that carries it, so keep that object unrotated, at scale 1 and
still, and bring bodies and colliders in with Object Info or Collection Info set to **Relative**. For many bodies at
once, see [Bodies](#bodies).

## Examples

Seven small scenes in one shared lab, one `.blend` each in [`examples/`](examples). Open a file and press play from
frame 1; its **README** text explains what to look at and what to try. The stills below are frames from the files
themselves, simulated from frame 1. In 04 and 05 the colour is the stiffness the solver used: blue is rigid, yellow is
soft.

<table>
<tr><th colspan="3" align="left"><a href="examples/01_stacking.blend">01 · Stacking</a>: rigid bodies land and stack</th></tr>
<tr>
<td width="33%" valign="top"><img src="docs/assets/examples/01_stacking_a.jpg" width="100%" alt="Frame 1, front view: a pyramid of cubes in three rows with 6 cm gaps between the rows."><br><sub>Three rows released 6 cm apart.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/01_stacking_b.jpg" width="100%" alt="Frame 5: the lower rows have landed and the top cube is still falling."><br><sub>They land one row after another.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/01_stacking_c.jpg" width="100%" alt="Frame 180, isometric view: the dropped pyramid, a 10-cube pyramid and a four-size podium, all at rest."><br><sub>Every top settles within 2 mm of exact.</sub></td>
</tr>
<tr><th colspan="3" align="left"><a href="examples/02_friction.blend">02 · Friction</a>: blocks on slopes, Friction 0.5</th></tr>
<tr>
<td width="33%" valign="top"><img src="docs/assets/examples/02_friction_a.jpg" width="100%" alt="Frame 1, isometric view: eight wedges from 20 to 34 degrees in a row, each with the same small block on its slope."><br><sub>Eight blocks on wedges from 20° to 34°.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/02_friction_b.jpg" width="100%" alt="Frame 20: the teal blocks on the three steepest wedges are sliding down."><br><sub>From 30° up, they slide.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/02_friction_c.jpg" width="100%" alt="Frame 120: the teal blocks lie on the floor in front of their wedges, and the orange blocks have not moved."><br><sub>Up to 28° they hold; Coulomb says 26.6°.</sub></td>
</tr>
<tr><th colspan="3" align="left"><a href="examples/03_press.blend">03 · Press</a>: an animated collider squeezes rubber</th></tr>
<tr>
<td width="33%" valign="top"><img src="docs/assets/examples/03_press_a.jpg" width="100%" alt="Frame 1, front view: a press with its steel plate raised above three rubber blocks, blue, purple and pink."><br><sub>Three rubber blocks under an animated press.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/03_press_b.jpg" width="100%" alt="Frame 70, close-up: the plate has squeezed the blocks to half their height, and they bulge sideways."><br><sub>Half height, 2 mm from plate and floor.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/03_press_c.jpg" width="100%" alt="Frame 150, isometric view: the plate is up again and the three blocks are cubes again."><br><sub>Released, they spring back within 1.1 mm.</sub></td>
</tr>
<tr><th colspan="3" align="left"><a href="examples/04_stiffness.blend">04 · Stiffness</a>: the dial from rigid to goo</th></tr>
<tr>
<td width="33%" valign="top"><img src="docs/assets/examples/04_stiffness_a.jpg" width="100%" alt="Frame 1, isometric view: six identical cubes in the air in a row, coloured from blue at stiffness 1 to yellow at 0.05."><br><sub>One cube at six stiffness values, 1 to 0.05.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/04_stiffness_b.jpg" width="100%" alt="Frame 9, front view: on impact the blue cube keeps its shape while the softer cubes squash flat."><br><sub>On impact, the soft ones squash.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/04_stiffness_c.jpg" width="100%" alt="Frame 120, isometric view: from a sharp cube, through a rounded cube, a slumped cube and two soft mounds, to a flat puddle."><br><sub>Settled: from a rigid cube to a puddle of goo.</sub></td>
</tr>
<tr><th colspan="3" align="left"><a href="examples/05_stiffness_map.blend">05 · Stiffness Map</a>: one plank, four painted maps</th></tr>
<tr>
<td width="33%" valign="top"><img src="docs/assets/examples/05_stiffness_map_a.jpg" width="100%" alt="Frame 1, isometric view: four identical straight planks, each across two supports and coloured by its stiffness map."><br><sub>Four identical planks; only the map differs.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/05_stiffness_map_b.jpg" width="100%" alt="Frame 120: the blue plank is straight, the pink one sags as a whole, the third sags only in its soft middle, and the fourth droops at its soft ends."><br><sub>Each bends only where its map is soft.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/05_stiffness_map_c.jpg" width="100%" alt="Frame 120, front view: the four sag profiles against the wall grid."><br><sub>Soft middle: sags 10.7 cm, ends stay flat.</sub></td>
</tr>
<tr><th colspan="3" align="left"><a href="examples/06_self_collision.blend">06 · Self Collision</a>: off (left) and on (right)</th></tr>
<tr>
<td width="33%" valign="top"><img src="docs/assets/examples/06_self_collision_a.jpg" width="100%" alt="Frame 1, isometric view: two napkins above two posts, each a blue sheet 2 cm above an orange sheet."><br><sub>Each is one body: a cover and a lining.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/06_self_collision_b.jpg" width="100%" alt="Frame 10: both napkins drape over their posts."><br><sub>Both drop over a post.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/06_self_collision_c.jpg" width="100%" alt="Frame 110: on the left the orange lining shows through the blue cover in patches; on the right the blue cover lies cleanly over the lining."><br><sub>Off, the layers cross; on, they stay apart.</sub></td>
</tr>
<tr><th colspan="3" align="left"><a href="examples/07_cloth.blend">07 · Cloth</a>: a soft body at stiffness 0.1</th></tr>
<tr>
<td width="33%" valign="top"><img src="docs/assets/examples/07_cloth_a.jpg" width="100%" alt="Frame 1, isometric view: a checked sheet hanging flat above a steel sphere and a yellow block."><br><sub>A 1.4 m sheet above a sphere and a block.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/07_cloth_b.jpg" width="100%" alt="Frame 8: the sheet has landed on the sphere and the block and starts to fold."><br><sub>It lands and starts to fold.</sub></td>
<td width="33%" valign="top"><img src="docs/assets/examples/07_cloth_c.jpg" width="100%" alt="Frame 110: the sheet drapes over the sphere and the block in soft folds down to the floor."><br><sub>Draped, it rests 3 mm above the floor.</sub></td>
</tr>
</table>

## The Node

The node as it opens, with its defaults. Each number on the left is explained on the right.

<table>
<tr>
<td width="400" valign="top"><img src="docs/assets/node.png" width="400" alt="The newdles node with its default values and its 24 inputs numbered from 1 to 24, top to bottom. 1 Geometry. Under Physics: 2 Forces (0, 0, 0), 3 Mass 1, 4 Substeps 4, 5 Linear Damping 0.05, 6 Angular Damping 0.05. Under Elasticity: 7 Stiffness 1, 8 Stiffness Attribute 'stiffness', 9 Spring Rate 100, 10 Grain 0.33, 11 Soft Iterations 3, 12 Stretch 0, 13 Deformation Damping 3, 14 UV Map 'UVMap'. Under Collision: 15 Colliders, 16 Thickness 0.002, 17 Max Depth 0.15, 18 Friction 0.6, 19 Contact Relaxation 1, 20 Body Contacts 3, 21 Body Contact Relaxation 0.6, 22 Self Collision off. Under Particles: 23 Proxy Density 60, 24 Include Vertices on. The outputs are Geometry, Particles and Body Points."></td>
<td valign="top">

1. **Geometry**: the bodies ([Bodies](#bodies)).

**Physics**

2. **Forces**: a force on every particle, divided by its body's mass. `(0, 0, -9.81)` is gravity.
3. **Mass**: each body's mass.
4. **Substeps**: steps per frame, 1 to 64. More are firmer and slower.
5. **Linear Damping**: slows all motion, per second.
6. **Angular Damping**: slows spinning, per second.

**Elasticity**

7. **Stiffness**: the dial from rigid to goo ([above](#stiffness-from-rigid-to-goo)).
8. **Stiffness Attribute**: the name of each body's map.
9. **Spring Rate**: how stiff *soft* is. Raise it for small things, lower it for big floppy ones.
10. **Grain**: the size of the patches a soft body bends between. At 2 it bends like a rope.
11. **Soft Iterations**: soft passes per substep, 1 to 16. More give firmer cloth and cleaner folds.
12. **Stretch**: how far the surface stretches ([above](#stiffness-from-rigid-to-goo)).
13. **Deformation Damping**: calms the wobble of soft bodies without slowing them down.
14. **UV Map**: copied onto the particles as `rb_uv`, so a texture can drive Stiffness.

**Collision**

15. **Colliders**: meshes to collide with, like floors and walls.
16. **Thickness**: the gap every contact rests at. Keep it small.
17. **Max Depth**: the deepest overlap that is still pushed out.
18. **Friction**: Coulomb friction, for colliders and bodies.
19. **Contact Relaxation**: scales the push off colliders, 0.1 to 2.
20. **Body Contacts**: partner bodies checked per particle, 0 to 32. 0 turns body collisions off.
21. **Body Contact Relaxation**: scales the pushes between bodies, 0.1 to 1.5. Keep it below 1.
22. **Self Collision**: soft bodies collide with themselves ([above](#self-collision)).

**Particles**

23. **Proxy Density**: extra particles per m² on active bodies, for large faces.
24. **Include Vertices**: the vertices as particles. Turn it off only for very dense meshes.

</td>
</tr>
</table>


## How it works

newdles is position-based dynamics with shape matching, built entirely from Geometry Nodes. Every frame:

1. **Your input becomes particles.** Each body's vertices, plus extra samples on its faces, become particles with a
   mass, a stiffness and a rest offset from the body's centre.
2. **The last frame carries over.** Each particle takes over the position and velocity of the nearest particle of
   the same body in the last frame, matched in the body's own rest space.
3. **The frame runs in Substeps**, each one as in the table below.
4. **The mesh follows its particles.** Every vertex takes its particle's position, and each body's centre, rotation
   and transform go to Body Points.

**One substep**

| Step | What happens |
|---|---|
| **Predict** | Particles move by their velocity and the forces. Passive particles go where their input is. |
| **Detect** | Contacts with colliders, other bodies and the body's own surface are found at the start positions. |
| **Enforce** | The predicted positions are pushed out to Thickness from every contact, with friction and the mass split. |
| **Soft passes** | Soft Iterations passes over the mesh edges, the overlapping patches and the self contacts. |
| **Shape match** | Each body's best-fit rigid pose is found, and every particle is pulled toward it by its stiffness. |
| **Re-project** | The stored contacts are applied again at the pulled positions: the rigid part of a body moves as one, the soft part particle by particle. |
| **Integrate** | Velocities come from the motion, followed by deformation and angular damping. |

When nothing active is soft, the soft passes and self collision are skipped entirely, so rigid scenes pay nothing
for them. The whole solver is 5,534 nodes and 9,102 links in one Simulation Zone. Repeat Zones run the substeps and
the soft passes, and three Matrix SVD nodes find the best-fit rotations. A Python script writes it all.

### Bodies

- **Every instance is one body**, however many pieces it has. A plain mesh is one body too, and a Collection Info
  node with *Separate Children* on makes every object in a collection its own body.
- **Passive bodies** follow their input exactly and push the active ones. Store a Boolean `active`, set to False,
  on the Instance domain.
- **The input may change every frame**: its shape, its vertex count or its number of bodies. The simulation carries
  over, and a new body starts where the input puts it.
- **Mass** is per body, 1 by default. Gravity pulls every body the same way; mass decides who gives way when bodies
  push each other.

### Stiffness, from rigid to goo

**Stiffness** is how well a body holds its shape: the dial on the node, times each body's own map.

| Stiffness | 1 | 0.9 | 0.5 | 0.1 | 0.01 | 0 |
|---|---|---|---|---|---|---|
| **Behaves like** | rigid | rubber | soft | cloth | goo | loose particles |

**Stretch** is how far a body's surface may stretch.

| Stretch | 0 | 0.03 | 0.3 | 1 |
|---|---|---|---|---|
| **The surface** | doesn't stretch | has half-strength edges | is a jelly skin | is goo, at constant volume |

**The map** is the float attribute named in **Stiffness Attribute**, from 0 (free) to 1 (rigid). Store it on the
points for a painted map (a vertex group works too), or on the instances for one value per body.

> [!IMPORTANT]
> Once any body has the map, a body without a value reads **0**: loose particles. Store 1 on the Instance domain of
> every body first; a body's own point map always wins over it.

### Contacts

Every contact comes to rest at **Thickness** from the surface it touches: the visible gap, 2 mm by default.
Contacts are found at the start of each substep, over a distance that grows with the motion, so fast impacts are
caught however thin that gap is.

- **Colliders** hold still within a frame, so make fast-moving obstacles passive bodies. A particle in a crease or a
  corner keeps both walls.
- **Bodies** split each push by mass, ground first, so stacks stand.
- **Friction** is Coulomb friction, against the motion of the surface being touched.

#### Self collision

With **Self Collision** on, soft bodies also keep Thickness from their own surface. One rule decides what may
collide: **only surfaces that had air between them at rest**, and the normals say which side is air.

| Collides | Doesn't collide |
|---|---|
| lip on teeth, eyelid on eyeball, the layers of a garment | a vertex's own neighbourhood, tooth roots in the gums |
| the walls of a hollow bag (inward normals) | the walls of a solid bag (outward normals), a flat sheet with itself |

## Limitations

- A thin soft flap pushed hard onto a rigid part of its own body can pass through it.
- Large faces can cut into curved colliders between their vertices: subdivide them.
- A flat sheet never collides with itself: model cloth layers with a gap.
- A very fast particle rounding a sharp collider edge can be held by the old face for one substep.
- Two rigid parts of one body never collide with each other.

## Credits

newdles builds on two classic papers:

- Matthias Müller, Bruno Heidelberger, Matthias Teschner and Markus Gross, *Meshless Deformations Based on Shape
  Matching*, SIGGRAPH 2005.
- Matthias Müller, Bruno Heidelberger, Marcus Hennix and John Ratcliff, *Position Based Dynamics*, Journal of Visual
  Communication and Image Representation, 2007.

---

*newdles is free software, licensed under the [GNU General Public License v3.0 or later](LICENSE)
(GPL-3.0-or-later).*

*This software is provided **"as is"**, without warranty of any kind, express or implied. You use it entirely at your own
risk. The author is **not responsible or liable** for any loss, damage, corrupted or lost work, or any other harm
arising from the use of this software. Always keep backups of your `.blend` files before using it.*

*newdles is partly vibe-coded with AI. A Python script writes the node group, and a headless test suite checks it
in Blender.*
