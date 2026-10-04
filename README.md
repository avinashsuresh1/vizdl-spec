# VizDL: Visual Design Language


[![Specification](https://img.shields.io/badge/spec-v1.0-blue.svg)](SPECIFICATION.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Physics: Energy_Conserving](https://img.shields.io/badge/physics-R%2BT%2BA%3D1.0-green.svg)](PRINCIPLES.md)

**VizDL** (Visual Design Language) is a declarative, physically unified visual modeling and animation language. It replaces the ad-hoc approximations, coordinate singularities, and unphysical paint models of traditional 3D graphics with first-principles **condensed-matter physics** and **coordinate-free Geometric Algebra ($Cl(3,0)$)**.

---

## The Core Tenets of VizDL

| Traditional 3D Graphics | VizDL Physical Design Language |
| :--- | :--- |
| **Euler Angles & Quaternions** (Gimbal lock, coordinate bias) | **$Cl(3,0)$ Geometric Algebra Motors ($M = TR$)** (Singularity-free, coordinate-free) |
| **Bézier Curves & Polygonal Meshes** (Ad-hoc control points, $C^1$ breaks) | **Intrinsic Curvature Profiles $\kappa(s) = \sum a_k s^k$** (Continuous $G^2$ curvature) |
| **Arbitrary RGB Surface Colors** (Energy leaks, cartoon shading) | **Condensed-Matter Triad $(E_g, \rho_e, \sigma) \in [0, 1]^3$** (Exact energy conservation $R+T+A = 1.0$) |
| **Synthetic Invisible Lights** (Points, directional cones floating in vacuum) | **Unified Radiance: Light is Material with Glow** ($\epsilon \in [0, 1]$ non-thermal excitation) |
| **Imperative Keyframe Splines** (Fragmented transform vectors) | **The 4 Pillars of Kinematics** (Trajectory Sweep, Axis Revolution, Morphing, Excitation) |

---

## Repository Map

- **[`SPECIFICATION.md`](SPECIFICATION.md)**: The authoritative, exhaustive declarative TOML schema for all VizDL entities.
- **[`PRINCIPLES.md`](PRINCIPLES.md)**: Deep mathematical, geometrical, and condensed-matter foundations of VizDL.
- **[`ARCHITECTURE.md`](ARCHITECTURE.md)**: Engine architecture, radiometric Linear-sRGB color space, and GPU shader pipeline.
- **[`docs/`](docs/)**: Comprehensive user guides:
  - [`docs/profiles.md`](docs/profiles.md): Curvature polynomials, Euler spirals, circular cross-sections.
  - [`docs/materials.md`](docs/materials.md): Condensed-matter physics, metals vs. dielectrics, optical conservation.
  - [`docs/meshes.md`](docs/meshes.md): Parametric surfaces of sweep and revolution.
  - [`docs/objects.md`](docs/objects.md): Hierarchical assemblies and motor positioning.
  - [`docs/animations.md`](docs/animations.md): The 4 pillars of motion and pacing curves.
  - [`docs/scenes.md`](docs/scenes.md): Scene timelines, director cameras, and self-illuminating worlds.
- **[`examples/`](examples/)**: Sample VizDL projects.

---

## Project Structure

A standard VizDL project is organized into modular TOML directories:

```
my_vizdl_project/
├── profiles/     # 1D/2D cross-sections, 3D paths, and timing curves
├── materials/    # Condensed-matter physical triads and active matter
├── meshes/       # 3D parametric surfaces (sweep and revolve)
├── objects/      # Hierarchical assemblies of components
├── animations/   # Kinematic tracks and quantum excitation modulations
└── scenes/       # Master timeline, camera placement, placed object instances
```

Every entity across all directories has a **mandatory, unique `name`** that serves as the universal identifier for composition.

---

## Minimal Walkthrough: A Glowing Celestial Orb

### 1. `profiles/circle.toml`
Defines a closed circular cross-section with constant curvature $c = 1.0$ (radius $R = 1/|c| = 1.0$):

```toml
name = "circle"
start_point = [1.0, 0.0, 0.0]
curvature = 1.0
```

### 2. `materials/luminescent_crystal.toml`
Defines a wide-bandgap dielectric material with electronic pumping:

```toml
name = "luminescent_crystal"
energy_gap = 0.95         # Transparent wide bandgap
electron_density = 0.35   # Optically pure crystal (IOR ~ 1.5)
roughness = 0.05          # Near-specular polished surface
excitation = 0.80         # Resonant radiant emission (self-glow)
```

### 3. `meshes/sphere.toml`
Revolves the circular profile around the Z-axis:

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
Composes the sphere into a positioned assembly:

```toml
name = "beacon"

[[components]]
name = "core"
mesh = "sphere"
scale = 1.5
```

### 5. `animations/orbital_spin.toml`
Defines an orbital revolution around the Y-axis:

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
Places the beacon in the master scene. Notice that **no artificial lights are declared**—the beacon itself illuminates the environment through its material excitation:

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

VizDL Specification is licensed under the [MIT License](LICENSE).
