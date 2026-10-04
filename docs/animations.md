# Animations in VizDL

Animations define time-dependent kinematics, topological mesh morphing, radiant luminescence modulation, and camera choreography.

---

## 1. The Four Pillars of Animation

In VizDL, animation is strictly partitioned into four physical and geometric pillars:

1. **Trajectory Sweep**: Moving an object along a 3D intrinsic curve path.
2. **Axis Revolution**: Orbiting or spinning an object around a directional axis.
3. **Shape Morphing**: Interpolating vertex positions between two congruent mesh assemblies.
4. **Excitation Glow Modulation**: Varying electronic pumping to cause radiant pulsing.

---

## 2. Schema Specification

```toml
name = "<string>"                 # Required. Unique animation identifier.
description = "<string>"          # Optional.
duration = <f64>                  # Required. Cycle duration in seconds. Range: > 0.0.
loop = <bool>                     # Optional. Default: false. Repeat animation cycle.
loop_start = <f64>                # Optional. Default: 0.0. Start timestamp of loop region in seconds.
loop_end = <f64>                  # Optional. Default: duration. End timestamp of loop region in seconds.

# --- Pillar 1: Curve Sweep OR Pillar 2: Axis Revolution ---
[motion]                          # Optional.
mode = "sweep" | "revolve"        # Optional. Default: "sweep".

# When mode = "sweep":
path = "<string>"                 # Required (for sweep mode). 3D trajectory curve in profiles/*.toml.
timing = "<string>"               # Optional. Default: "linear". Pacing curve in profiles/*.toml or easing.

# When mode = "revolve":
axis = "x" | "y" | "z" | [<f64>, <f64>, <f64>] # Optional. Default: "z". Revolution axis direction vector.
angle = <f64>                     # Optional. Default: 360.0. Total revolution angle in degrees.
offset = <f64>                    # Optional. Default: 0.0. Orbital radius from revolution axis.
timing = "<string>"               # Optional. Default: "linear". Pacing curve in profiles/*.toml or easing.

# --- Pillar 3: Shape Morphing ---
[morph]                           # Optional.
target_object = "<string>"        # Required (if morph block present). Target object in objects/*.toml.
timing = "<string>"               # Optional. Default: "linear". Pacing curve in profiles/*.toml or easing.

# --- Pillar 4: Excitation Glow Modulation ---
[excitation]                      # Optional.
start = <f64>                     # Required (if excitation block present). Excitation at start. Range: [0.0, 1.0].
end = <f64>                       # Required (if excitation block present). Excitation at end. Range: [0.0, 1.0].
timing = "<string>"               # Optional. Default: "linear". Pacing curve in profiles/*.toml or easing.

# --- Director Camera Track ---
[camera]                          # Optional.
mode = "sweep" | "revolve"        # Optional. Default: "sweep".

# When mode = "sweep":
path = "<string>"                 # Optional. Camera position trajectory in profiles/*.toml.
target_path = "<string>"          # Optional. Camera look-at trajectory in profiles/*.toml.
target = [<f64>, <f64>, <f64>]    # Optional. Static look-at target position [x, y, z].

# When mode = "revolve":
axis = "x" | "y" | "z" | [<f64>, <f64>, <f64>] # Optional. Default: "z". Orbit axis direction vector.
target = [<f64>, <f64>, <f64>]    # Optional. Orbit center / look-at target [x, y, z].
angle = <f64>                     # Optional. Default: 360.0. Total orbit angle in degrees.
offset = <f64>                    # Optional. Default: 0.0. Orbit radius from target.

fov_start = <f64>                 # Optional. Initial field of view in degrees. Range: (0.0, 180.0).
fov_end = <f64>                   # Optional. Final field of view in degrees. Range: (0.0, 180.0).
timing = "<string>"               # Optional. Default: "linear". Pacing curve in profiles/*.toml or easing.
```

---

## 3. Pacing Timing Curves

The `timing` parameter determines how normalized time $t / \text{duration} \in [0, 1]$ maps to progression parameter $u \in [0, 1]$.

- **Standard Built-in Easings**:
  - `"linear"`: Constant progression rate $u = \tau$.
  - `"ease_in"`: Smooth acceleration $u = \tau^2$.
  - `"ease_out"`: Smooth deceleration $u = 1 - (1 - \tau)^2$.
  - `"ease_in_out"`: Smooth cubic S-curve.
- **Custom Profile Timing Curves**:
  Any 2D profile from `profiles/*.toml` can be used as a custom timing curve, providing arbitrary physical acceleration and deceleration profiles.

---

## 4. Examples

### Orbital Revolution with Excitation Surge
An object revolving around the Z-axis while surging from passive ground state ($\epsilon = 0.0$) to intense radiant bloom ($\epsilon = 1.0$):

```toml
name = "orbital_surge"
duration = 6.0
loop = true

[motion]
mode = "revolve"
axis = "z"
angle = 360.0
offset = 5.0
timing = "ease_in_out"

[excitation]
start = 0.0
end = 1.0
timing = "ease_in"
```

### Director Orbiting Camera
A camera smoothly orbiting a central subject:

```toml
name = "cinematic_orbit"
duration = 12.0
loop = true

[camera]
mode = "revolve"
axis = "y"
target = [0.0, 0.0, 0.0]
angle = 360.0
offset = 8.0
fov_start = 55.0
fov_end = 40.0 # Slow zoom-in during orbit
timing = "linear"
```
