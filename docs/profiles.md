# Profiles in VizDL

Profiles define 2D cross-sections for mesh generation, 3D trajectory curves for sweeps and camera paths, and 1D/2D timing curves for animation pacing.

---

## 1. Intrinsic Curvature Formulation

In VizDL, curves are defined by their intrinsic curvature $\kappa(s)$ along their arc length $s$:

$$\kappa(s) = \sum_{k=0}^n a_k s^k = a_0 + a_1 s + a_2 s^2 + \dots$$

This formulation provides significant advantages over traditional Bézier and NURBS representations:
- **Coordinate-Free**: The shape is defined by its intrinsic curvature geometry rather than dependent control vertices.
- **Continuous Curvature ($G^2$)**: Eliminates curvature breaks that cause optical distortion when sweeping reflective surfaces.
- **Unified**: Lines, circles, clothoid spirals, and arbitrary splines share the identical mathematical framework.

---

## 2. Schema Specification

```toml
name = "<string>"                 # Required. Unique profile identifier.
description = "<string>"          # Optional. Human-readable description.
start_point = [<f64>, <f64>, <f64>] # Required. 3D start coordinate [x, y, z].
end_point = [<f64>, <f64>, <f64>]   # Optional. 3D end coordinate [x, y, z]. If omitted, curve is closed.

# Curvature specification. One of the following three formats:
# Format A: Constant curvature scalar
curvature = <f64>

# Format B: Polynomial coefficient array [a0, a1, a2, ...]
# curvature = [<f64>, <f64>, ...]

# Format C: Explicit power-coefficient terms
# curvature = [
#   { power = <u32>, coeff = <f64> },
#   ...
# ]
```

---

## 3. Geometric Profiles Guide

### 3.1 Straight Line
A straight line has zero curvature everywhere:

```toml
name = "straight_line"
start_point = [0.0, 0.0, 0.0]
end_point = [0.0, 10.0, 0.0]
curvature = 0.0
```

### 3.2 Circle (Closed Periodic Loop)
A circle has constant non-zero curvature $c = 1/R$. When `end_point` is omitted, the curve automatically evaluates as a closed periodic loop of radius $R = 1/|c|$ and circumference $L = 2\pi R$:

```toml
name = "unit_circle"
start_point = [1.0, 0.0, 0.0]
curvature = 1.0 # Radius = 1.0, closed 360-degree loop
```

### 3.3 Circular Arc
To create an open circular arc, simply specify the `end_point`:

```toml
name = "quarter_arc"
start_point = [1.0, 0.0, 0.0]
end_point = [0.0, 1.0, 0.0]
curvature = 1.0
```

### 3.4 Euler Spiral (Clothoid)
An Euler spiral features linear curvature progression $\kappa(s) = a_1 s$. The radius of curvature decreases smoothly with distance, providing perfect $G^2$ transition curves without curvature shock:

```toml
name = "clothoid_transition"
start_point = [0.0, 0.0, 0.0]
end_point = [5.0, 2.5, 0.0]
curvature = [0.0, 0.25] # a0 = 0.0, a1 = 0.25
```

---

## 4. Multi-Curve Composites

When cross-sections require piecewise composition (such as a D-shape with a straight side and a semicircular arc), use `[[curves]]`:

```toml
name = "d_shape"

[[curves]]
start_point = [0.0, -1.0, 0.0]
end_point = [0.0, 1.0, 0.0]
curvature = 0.0 # Straight backbone

[[curves]]
start_point = [0.0, 1.0, 0.0]
end_point = [0.0, -1.0, 0.0]
curvature = 1.0 # Semicircular bulge
```
