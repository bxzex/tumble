# Tumble

A 2D rigid body physics engine written from nothing. Convex polygons and
circles, separating axis collision detection, a sequential impulse solver,
friction, restitution and sleeping.

Live: https://bxzex.github.io/tumble/

## The pipeline

**Mass properties** come from the polygon's own area integrals, so a long plank
genuinely resists rotation more than a square of the same mass, and every shape
is recentred on its true centroid.

**Broad phase** sweeps axis aligned bounding boxes and discards pairs that
cannot touch, including any pair where both bodies are asleep.

**Narrow phase** is a separating axis test that returns more than a yes or no.
It picks the reference face, finds the incident face most squarely facing it,
clips that face against the reference side planes, and gives every surviving
point its own penetration depth. That last detail is what stops a resting box
from being pushed harder at one corner than the other, which is the difference
between a stack and a slow motion collapse.

**Solver** is sequential impulse with three pieces that matter:

- *Accumulated impulses*, clamped so the total normal impulse can never pull
  two bodies together, and friction clamped to the Coulomb cone around it.
- *Split impulse*: overlap is pushed out through a separate pseudo velocity that
  moves position but is never added to the body's real motion. Feeding the
  correction through ordinary velocity, as plain Baumgarte does, quietly adds
  energy, and a pyramid built that way will eventually throw itself apart. Mine
  did, which is how I found it.
- *Warm starting*: each contact point's impulses are matched to the previous
  frame's by position and applied before the iteration loop. Without it the
  solver restarts from zero every frame and never converges. Adding it dropped
  the resting kinetic energy of a 45 box pyramid from around 0.3 to 0.001.

**Sleeping** takes bodies out of the solve once they have not travelled
appreciably for half a second, and a sleeping body becomes immovable so it acts
as ground for whatever rests on it. A body only sleeps once its contact
neighbours are also at rest, so an island settles together. Loose scenes quiet
down well; a dense 45 body pyramid keeps micro-settling and re-waking itself,
which is honest behaviour for an engine with no island solver.

## Verification

Every scene is checked for containment (nothing tunnels out of the arena) and
resting energy. Solve time stays between 0.1 and 0.6 ms for 70 bodies with
around 150 contact points.

## Notes

One HTML file. No libraries, no physics engine, no build step. Click empty space
to drop a body, drag one to throw it, right click to set off an explosion.

Built by [bxzex](https://bxzex.com).
