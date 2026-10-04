# VizDL Specification (Version 1.0)

VizDL (Visual Design Language) is a declarative format for describing 3D shapes, physical materials, and animations using structured TOML files.

This repository shares the Version 1.0 specification of the file schemas and the ideas behind the model.

---

## What is VizDL?

VizDL describes 3D scenes using a few foundational concepts:

1. **Curves Defined by Curvature**: Instead of placing control points, curves are described by how much they bend along their length. A straight line has zero curvature, a circle has constant curvature, and a spiral bends progressively.
2. **Surfaces from Curves**: 3D shapes are created either by sweeping a 2D profile along a curve or by revolving it around an axis.
3. **Materials from Physical Properties**: Rather than picking arbitrary RGB colors, materials are defined by basic physical properties (energy gap, electron density, roughness) normalized between 0 and 1. Materials obey conservation of energy ($R + T + A = 1$).
4. **Light as Glowing Matter**: Light is emitted directly by objects whose materials have an excitation value greater than zero, rather than through standalone light entities.
5. **Animation Through Four Core Motions**: Movement is organized into four basic types: sweeping along a path, revolving around an axis, morphing between shapes, and changing a material's glow.
6. **Clear Identifiers**: Every curve, material, mesh, object, and animation has a mandatory `name` so parts can reference one another reliably.

---

## Repository Structure

- **[`SPECIFICATION.md`](SPECIFICATION.md)**: The full TOML schema specification with all fields, types, and defaults.
- **[`PRINCIPLES.md`](PRINCIPLES.md)**: An explanation of the mathematical and physical ideas behind the model.
- **[`docs/`](docs/)**: Guides covering each part of a VizDL project:
  - [`docs/profiles.md`](docs/profiles.md): Defining curves and cross-sections.
  - [`docs/materials.md`](docs/materials.md): Defining materials and physical properties.
  - [`docs/meshes.md`](docs/meshes.md): Creating 3D shapes by sweep or revolution.
  - [`docs/objects.md`](docs/objects.md): Assembling meshes into composite objects.
  - [`docs/animations.md`](docs/animations.md): Setting up motion, morphing, and glow effects.
  - [`docs/scenes.md`](docs/scenes.md): Putting objects, cameras, and timelines into a scene.
- **[`examples/`](examples/)**: Sample project files showing how everything connects together.

---

## Project Layout

A standard VizDL project organizes files into six folders:

```
project/
├── profiles/     # 2D cross-sections and 3D paths
├── materials/    # Physical material definitions
├── meshes/       # 3D shapes made by sweep or revolve
├── objects/      # Assemblies of meshes with positions and scales
├── animations/   # Motion paths, rotations, and glow changes
└── scenes/       # Timelines, cameras, and placed objects
```

---

## Quick Example: A Glowing Orb in Orbit

Here is how a simple project looks across the files:

### 1. `profiles/circle.toml`
A closed circle with curvature 1.0 (radius 1.0):
```toml
name = "circle"
start_point = [1.0, 0.0, 0.0]
curvature = 1.0
```

### 2. `materials/luminescent_crystal.toml`
A clear, smooth material with a glowing excitation value:
```toml
name = "luminescent_crystal"
energy_gap = 0.95         # Wide gap (transparent)
electron_density = 0.35   # Low density (glass/crystal range)
roughness = 0.05          # Smooth surface
excitation = 0.80         # Emits light
```

### 3. `meshes/sphere.toml`
Revolves the circle around the Z axis to form a sphere:
```toml
name = "sphere"
surface = "circle"
material = "luminescent_crystal"

[path]
mode = "revolve"
axis = "z"
angle = 360.0
```

### 4. `objects/beacon.toml`
Creates an object from the sphere mesh:
```toml
name = "beacon"

[[components]]
name = "core"
mesh = "sphere"
scale = 1.5
```

### 5. `animations/orbital_spin.toml`
Spins the object in a circle around the Y axis:
```toml
name = "orbital_spin"
duration = 8.0
loop = true

[motion]
mode = "revolve"
axis = "y"
angle = 360.0
offset = 4.0
```

### 6. `scenes/main.toml`
Places the beacon in the scene with a camera. Because the beacon's material has `excitation = 0.80`, it acts as its own light source:
```toml
name = "main"
duration = 8.0
fps = 60.0
loop = true

[camera]
position = [0.0, 5.0, 10.0]
target = [0.0, 0.0, 0.0]
fov = 45.0

[[objects]]
name = "beacon_instance"
object = "beacon"
animation = "orbital_spin"
```

---

## License

VizDL Specification Version 1.0 is shared under the [MIT License](LICENSE).
