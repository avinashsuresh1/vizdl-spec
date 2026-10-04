# Principles of VizDL (Version 1.0)

This document explains the core ideas behind VizDL (Visual Design Language). It covers how curves, 3D shapes, materials, light, and motion are modeled.

---

## 1. Overview

VizDL is built around a simple premise: describing 3D scenes using consistent physical and geometric ideas. 

Rather than treating shapes as arbitrary meshes, colors as arbitrary paints, and lights as invisible points in space, VizDL connects these concepts:
- **Curves** are described by how much they bend (curvature).
- **Surfaces** are made by sweeping or revolving curves.
- **Rotations** happen in 2D planes, avoiding alignment problems like gimbal lock.
- **Materials** are defined by physical properties (energy gap, electron density, roughness) that conserve energy ($R + T + A = 1$).
- **Light** is emitted by excited matter rather than separate light bulbs.
- **Motion** is organized into four basic transformations.

---

## 2. Curves Defined by Curvature

When describing a curve, computer graphics often uses control points (such as in Bézier curves). VizDL takes an intrinsic approach: describing how much the curve bends at each step along its length.

### The Driving Analogy
Imagine driving a car down a road at a steady speed:
- If you hold the steering wheel perfectly straight, you drive in a **straight line** (curvature = 0).
- If you hold the steering wheel turned at a constant angle, you drive in a **circle** (constant curvature $c$, radius $R = 1/|c|$).
- If you smoothly turn the steering wheel from straight into a curve, you drive along an **Euler spiral (clothoid)**, where the curve bends progressively without sudden jerks.

### Curvature Polynomials
In VizDL, curvature $\kappa(s)$ along arc length $s$ is defined by a polynomial:

$$\kappa(s) = a_0 + a_1 s + a_2 s^2 + \dots$$

- **Straight line**: $a_0 = 0.0$
- **Circle**: $a_0 \neq 0.0$ (and if no end point is specified, it loops into a full circle)
- **Transition spiral**: $a_1 \neq 0.0$ (curvature changes steadily along the path)

Because curvature is intrinsic to the path itself, shapes do not depend on external coordinate grids and have smooth curvature transitions everywhere.

---

## 3. Rotations in Planes

When an object turns in 3D space, standard Euler angles (yaw, pitch, roll) can suffer from *gimbal lock*, where two rotation axes line up and you lose a degree of freedom.

VizDL describes orientations as rotations within flat 2D planes:
$$\text{rotation} = [\theta_{xy}, \theta_{yz}, \theta_{xz}]$$

- $\theta_{xy}$: Rotation in the horizontal $xy$-plane (around the $z$-axis)
- $\theta_{yz}$: Rotation in the vertical $yz$-plane (around the $x$-axis)
- $\theta_{xz}$: Rotation in the lateral $xz$-plane (around the $y$-axis)

Behind the scenes, these planar rotations map to **rotors** in 3D Geometric Algebra ($Cl(3,0)$). When combined with translation, they form a **motor** ($M = T R$) that moves and turns objects cleanly in any direction without coordinate singularities.

---

## 4. Materials and the Physical Triad

In VizDL, materials do not use arbitrary RGB color pickers. Instead, material appearance comes from three physical properties, each normalized between **0.0 and 1.0**:

```toml
[materials]
energy_gap = <f64>        # Bandgap energy: determines metal vs dielectric
electron_density = <f64>  # Density: determines refractive index and transparency
roughness = <f64>         # Microfacet roughness: determines mirror vs diffuse
excitation = <f64>        # Optional: electronic pumping that makes material glow
```

### 4.1 Energy Gap ($E_g \in [0.0, 1.0]$)
The energy gap represents the threshold needed to excite an electron:
- **Metals / Conductors ($E_g \le 0.05$)**: The material has free electrons that form a reflective surface. Light cannot pass through ($T = 0$), resulting in strong metallic reflection ($R \approx 0.85 - 0.98$).
- **Dielectrics / Insulators ($E_g > 0.05$)**: Electrons are held in place. Photons with energy lower than the gap pass through freely:
  - *Wide gap ($E_g \ge 0.90$)*: All visible light passes through, producing transparent glass, crystal, or quartz.
  - *Narrower gap ($0.05 < E_g < 0.90$)*: Higher-energy blue and green photons are absorbed while lower-energy red photons pass through or scatter, producing natural material colors like amber, gold, or crimson.

### 4.2 Electron Density ($\rho_e \in [0.0, 1.0]$)
Electron density determines how strongly light bends when entering the material (refractive index $n = \sqrt{1 + 1.42 \rho_e}$):
- **Clear / Transmissive ($\rho_e \le 0.40$)**: Low density allows light to travel through clearly (glasses, resins, crystals).
- **Opaque Bulk ($\rho_e > 0.40$)**: Higher density causes light to scatter repeatedly inside the material, making it opaque ($T = 0$) like ceramics, stones, and dense plastics.

### 4.3 Surface Roughness ($\sigma \in [0.0, 1.0]$)
Controls how smooth the surface is:
- $\sigma = 0.0$: Perfectly smooth, mirror-like specular reflections.
- $\sigma = 1.0$: Rough surface with soft, diffuse scattering.

### 4.4 Conservation of Energy
Under all circumstances, materials follow thermodynamic conservation:
$$\text{Reflected} + \text{Transmitted} + \text{Absorbed} = 1.0$$
Energy is never artificially created or lost.

---

## 5. Light as Glowing Matter

In nature, light is not an invisible mathematical point floating in empty space; it comes from physical objects whose atoms are excited—such as a hot filament, an LED diode, or the sun.

In VizDL, **there are no standalone light entities**. Instead, light is produced by any regular object whose material has an `excitation` value greater than zero ($\epsilon > 0$):

- $\epsilon = 0.0$: Regular passive material.
- $\epsilon > 0.0$: The material glows, emitting light into the scene at its characteristic wavelength.

To create a lamp, laser, or star in VizDL, you simply place an object in the scene and assign it a material with non-zero excitation.

---

## 6. The Four Pillars of Animation

All movement and changes over time fit into four basic categories:

1. **Sweep**: Moving an object along a 3D curve path.
2. **Revolve**: Spinning or orbiting an object around a directional axis.
3. **Morph**: Smoothly blending the shape of one mesh into another.
4. **Excitation**: Changing how brightly a material glows over time (e.g. pulsing or fading).

### Timing and Pacing
Every animation has a `duration` and a `timing` parameter. Timing controls how the motion progresses over time:
- Standard easings like `"linear"`, `"ease_in"`, `"ease_out"`, or `"ease_in_out"`.
- Or any custom 2D curve profile from `profiles/`, allowing fine-grained control over acceleration and deceleration.

Animations can also define loop ranges (`loop`, `loop_start`, `loop_end`) so that repeating sections can be controlled directly inside the animation file.

---

## 7. Composition and Clean Identifiers

In VizDL, every profile, material, mesh, object, animation, and scene has a mandatory `name`:

```toml
name = "<string>" # Required on every entity
```

This makes project composition straightforward and explicit:
- A **Mesh** references a profile by name (`surface = "circle"`).
- An **Object Component** references a mesh by name (`mesh = "cylinder"`).
- An **Animation** references a curve by name (`path = "flight_path"`).
- A **Scene** references objects and animations by name (`object = "satellite"`, `animation = "orbit"`).

Each piece can be inspected, tested, and reused independently.
