# Objects in VizDL

Objects define hierarchical 3D assemblies composed of positioned, oriented, and scaled mesh components.

---

## 1. Compositional Architecture

An object is an assembly of components. Rather than storing flat vertex arrays, VizDL objects maintain a clean, declarative scene-graph structure where each sub-component references a modular mesh and an optional material override.

```toml
name = "<string>"                 # Required. Unique assembly identifier.
description = "<string>"          # Optional.
material = "<string>"             # Optional. Fallback material for components omitting material.

[[components]]
name = "<string>"                 # Required. Unique component identifier within the assembly.
mesh = "<string>"                 # Required. Identifier referencing mesh in meshes/*.toml.
material = "<string>"             # Optional. Material override identifier referencing materials/*.toml.
position = [<f64>, <f64>, <f64>]  # Optional. Default: [0.0, 0.0, 0.0]. 3D offset [x, y, z].
rotation = [<f64>, <f64>, <f64>]  # Optional. Default: [0.0, 0.0, 0.0]. Planar rotations [xy, yz, xz] in degrees.
scale = <f64> | [<f64>, <f64>, <f64>] # Optional. Default: 1.0. Uniform factor or per-axis [sx, sy, sz].
```

---

## 2. Planar Rotations via Geometric Algebra

Component orientations are specified as three planar rotation angles:
$$\text{rotation} = [\theta_{xy}, \theta_{yz}, \theta_{xz}]$$

- $\theta_{xy}$: Rotation within the $xy$-plane (bivector $e_{12}$, around the $Z$-axis)
- $\theta_{yz}$: Rotation within the $yz$-plane (bivector $e_{23}$, around the $X$-axis)
- $\theta_{xz}$: Rotation within the $xz$-plane (bivector $e_{31}$, around the $Y$-axis)

These planar rotations are evaluated directly into Geometric Algebra Rotors:
$$R = R_{xz} R_{yz} R_{xy}$$
This formulation guarantees coordinate invariance and prevents gimbal lock across all orientations.

---

## 3. Example: Multistage Rocket Assembly

A complete object demonstrating modular components, individual offsets, and material overrides:

```toml
name = "orbital_probe"
description = "Deep-space exploration probe with high-gain dish and scientific sensors"
material = "dark_slate" # Default chassis material

# Main central bus
[[components]]
name = "main_bus"
mesh = "cylinder_hull"
position = [0.0, 0.0, 0.0]
scale = [1.0, 1.0, 2.5] # Per-axis elongation along Z

# Highly reflective parabolic communication dish
[[components]]
name = "antenna_dish"
mesh = "parabolic_dish"
material = "polished_metal" # Override to high-reflectance conductor
position = [0.0, 0.0, 2.8]
rotation = [0.0, 45.0, 0.0] # 45-degree elevation tilt
scale = 1.2

# Quantum optical beacon
[[components]]
name = "navigation_beacon"
mesh = "micro_sphere"
material = "radiant_beacon" # Override to glowing material (excitation > 0)
position = [0.0, 0.0, 3.2]
scale = 0.3
```
