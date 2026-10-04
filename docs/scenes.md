# Scenes in VizDL

Scenes define the master director timeline, camera placement, and placed object instances in the world.

---

In VizDL, illumination radiates directly from objects in the scene whose materials have positive excitation ($\epsilon > 0.0$). There are no separate light primitives. A scene consists solely of:
1. Master timeline and clock settings.
2. The director camera.
3. Placed object instances.

---

## 2. Schema Specification

```toml
name = "<string>"                 # Required. Unique scene identifier.
description = "<string>"          # Optional.
duration = <f64>                  # Required. Scene duration in seconds. Range: > 0.0.
fps = <f64>                       # Optional. Default: 60.0. Frame evaluation rate.
loop = <bool>                     # Optional. Default: false. Repeat scene timeline.
loop_start = <f64>                # Optional. Default: 0.0. Start timestamp of scene loop region in seconds.
loop_end = <f64>                  # Optional. Default: duration. End timestamp of scene loop region in seconds.

[camera]                          # Optional. Scene camera.
position = [<f64>, <f64>, <f64>]  # Optional. Default: [0.0, 0.0, 5.0]. Static camera position [x, y, z].
target = "<string>" | [<f64>, <f64>, <f64>] # Optional. Tracked object instance identifier or static [x, y, z] target.
fov = <f64>                       # Optional. Default: 50.0. Field of view in degrees. Range: (0.0, 180.0).
animation = "<string>"            # Optional. Identifier referencing camera track animation in animations/*.toml.

[[objects]]                       # Required (at least one). Placed object instances.
name = "<string>"                 # Required. Unique instance identifier within the scene.
object = "<string>"               # Required. Identifier referencing object in objects/*.toml.
position = [<f64>, <f64>, <f64>]  # Optional. Default: [0.0, 0.0, 0.0]. Placement offset [x, y, z].
rotation = [<f64>, <f64>, <f64>]  # Optional. Default: [0.0, 0.0, 0.0]. Planar rotations [xy, yz, xz] in degrees.
scale = <f64> | [<f64>, <f64>, <f64>] # Optional. Default: 1.0. Uniform scale factor or per-axis [sx, sy, sz].
animation = "<string>"            # Optional. Identifier referencing animation in animations/*.toml.
start_time = <f64>                # Optional. Default: 0.0. Start timestamp on scene timeline in seconds.
```

---

## 3. Self-Illuminating Scene Example

Here is a complete solar system scene with a self-radiant central star and orbiting planetary bodies. Notice that the star provides physical illumination to the planets through its material excitation:

```toml
name = "planetary_system"
description = "A central radiant star with orbiting terrestrial and metallic planets"
duration = 16.0
fps = 60.0
loop = true

[camera]
position = [0.0, 12.0, 24.0]
target = [0.0, 0.0, 0.0]
fov = 45.0
animation = "cinematic_orbit"

# Central self-illuminating star (illuminates the entire system)
[[objects]]
name = "helios"
object = "star_body" # Star object whose material has excitation = 1.0
position = [0.0, 0.0, 0.0]
scale = 2.0

# Inner metallic planet
[[objects]]
name = "inner_planet"
object = "iron_orb" # Passive metal (energy_gap = 0.01, excitation = 0.0)
animation = "inner_orbit"
start_time = 0.0

# Outer crystal planet
[[objects]]
name = "outer_planet"
object = "crystal_orb" # Passive dielectric (energy_gap = 0.95, excitation = 0.0)
animation = "outer_orbit"
start_time = 0.0
```
