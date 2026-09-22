# Tumble

A 2D rigid body physics engine written from scratch, with boxes, polygons and circles stacking and tumbling.

https://bxzex.github.io/tumble/

Collision uses a separating axis test with proper contact clipping, so a box resting on the ground gets two contact points instead of one. The solver is sequential impulse with friction, warm starting and split impulse.

Two bugs taught me the most. Without warm starting the stacks never settled. With position correction fed through velocity, the pyramid slowly gained energy and threw itself apart. Fixing both took the resting energy of a 45-box pyramid from about 0.3 down to 0.001.

Click to drop a body, drag to throw one, right-click for an explosion.
