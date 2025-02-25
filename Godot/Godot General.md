# Bodies

|          | Area | StaticBody | CharacterBody | RigidBody |
| -------- | :--: | :--------: | :-----------: | :-------: |
| Collides |      |     X      |       X       |     X     |
| Moves    | Code |            |     Code      |  Physics  |
- **Area**: Just an area (does not collide), good for collision detection.
- **StaticBody**: Body that can collide but not move.
- **CharacterBody**: Body that can collide and move via code.
- **RigidBody**: Body that can collide and move via physics.
## Colliders
- **CollisionShape**: Attach to areas and bodies to define an area to detect collisions.
- **CollisionPolygon**: As CollisionShape but customizable.

# Signals
Attach signals to scripts to receive notifications when they emit.

## Custom Signals
Use the `signal` keyword to define a custom signal. You can then attach it to other scripts like normal and emit it with `signal.emit()`.

# Timers
Timers are nodes that emit a `timeout` signal based on user-defined parameters. They can be started and stopped with `timer.start()` and `timer.stop()`.
# Instantiate Scenes via Script
The process for instantiating scenes dynamically is as follows:
1. Load the scene with `load(scene)`
2. Instantiate the scene with `scene.instantiate()`
3. Attach the scene to the scene graph with `add_child(scene)`

# Random Number Generator
Create a new RNG with `RandomNumberGenerator.new()`.
Use various methods on the RNG to generate numbers.

# Viewport
Access viewport with `get_viewport()` and the visible rectangle with `viewport.get_visible_rect()`.

# Editor Values
We can access values in the editor by exporting them with the `@export` decorator.

# Loading Resources
All resources should be loadable using `load(resource_path)`.

## Custom Resources
Like scriptable objects (Unity), look into this.

# Collision Layers & Masks
Layers define what layer a body is on, mask defines what layers the object can detect interactions with.

In the project settings, we can update the names for different layers and masks.

# Markers
Use Marker nodes to create in-editor markers for marking positions used by code.

# Cooldown
Consider delta? Timer and signal works, but a cooldown manager using deltas may be better.

# Animations
- Tweens
- AnimatedSprite (2D or 2D in 3D)
- AnimationPlayer
## Tweens
`create_tween`
`tween.tween_property()`
Scale properties mathematically.

# Controls
CanvasLayer nodes for UI
Label node
Containers for UI elements
MarginContainer
HBox Container
TextureRect