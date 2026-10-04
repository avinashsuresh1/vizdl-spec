# VizDL Architecture & Rendering Pipeline

Authoritative system architecture and rendering pipeline specification for VizDL implementations.

---

## 1. Architectural Overview

VizDL is designed as a high-performance, pure-Rust graphics and modeling system (`vml_core`) built on top of `wgpu` (WebGPU for native and web). It eliminates the limitations, non-linear color bugs, and split-brain abstractions of legacy web engines by compiling declarative VizDL TOML directly into GPU vertex buffers and shader pipelines.

```
+-------------------------------------------------------------+
|               Declarative VizDL TOML Project                |
|  (profiles/, materials/, meshes/, objects/, animations/,   |
|   scenes/)                                                  |
+------------------------------+------------------------------+
                               |
                               v
+-------------------------------------------------------------+
|                  Parser & Validation Layer                  |
|  - Strictly validated mandatory names & referential graph   |
|  - Deserialization into strongly typed Rust structs         |
+------------------------------+------------------------------+
                               |
                               v
+-------------------------------------------------------------+
|                     Evaluation Engine                       |
|  - Curvature integration (Frenet-Serret frame)              |
|  - Parametric Sweep / Revolve mesh generation               |
|  - Cl(3,0) Geometric Algebra Motor transforms (M = T R)    |
|  - Kinematic timeline evaluation (4 Pillars of Animation)   |
+------------------------------+------------------------------+
                               |
                               v
+-------------------------------------------------------------+
|                   Optics & Radiometric Layer                |
|  - Condensed-matter triad to optical parameters             |
|  - IEC 61966-2-1 Linear-sRGB conversion (zero gamma leaks)  |
|  - Energy conservation verification (R + T + A = 1.0)       |
+------------------------------+------------------------------+
                               |
                               v
+-------------------------------------------------------------+
|                Native wgpu Rendering Pipeline               |
|  - WGSL Physical Cook-Torrance Shader (vizdl_pbr.wgsl)      |
|  - Drude metallic plasma & Beer-Lambert dielectric branches |
|  - Resonant quantum luminescence (Self-Glow Bloom)          |
|  - Headless Offscreen Buffer or Native Presentation Window  |
+-------------------------------------------------------------+
```

---

## 2. Radiometric Linear-sRGB Pipeline

A frequent failure mode in computer graphics is applying physical equations directly to non-linear sRGB values, causing washed-out colors, desaturated reds, and incorrect luminance summation.

VizDL enforces exact **IEC 61966-2-1** color space transforms at all boundaries:

### Non-linear to Linear-sRGB
For each color component $C_{\text{srgb}} \in [0.0, 1.0]$:
$$C_{\text{linear}} = \begin{cases} \frac{C_{\text{srgb}}}{12.92}, & C_{\text{srgb}} \le 0.04045 \\ \left(\frac{C_{\text{srgb}} + 0.055}{1.055}\right)^{2.4}, & C_{\text{srgb}} > 0.04045 \end{cases}$$

### Linear to Non-linear sRGB (Display Output)
$$C_{\text{srgb}} = \begin{cases} 12.92 \cdot C_{\text{linear}}, & C_{\text{linear}} \le 0.0031308 \\ 1.055 \cdot C_{\text{linear}}^{1/2.4} - 0.055, & C_{\text{linear}} > 0.0031308 \end{cases}$$

All internal calculations—albedo, Fresnel reflection, dielectric absorption, and emission flux—operate strictly in radiometric Linear-sRGB.

---

## 3. GPU Data Layout & Shader Uniforms

VizDL passes material and geometric properties to the GPU using 16-byte aligned, zero-copy uniform buffers (`bytemuck::Pod`):

```rust
#[repr(C)]
#[derive(Copy, Clone, Debug, bytemuck::Pod, bytemuck::Zeroable)]
pub struct GpuMaterial {
    pub linear_albedo: [f32; 4], // [r, g, b, 1.0] in Linear-sRGB
    pub properties: [f32; 4],    // [roughness, metalness, transmission, ior]
    pub emission: [f32; 4],      // [er, eg, eb, excitation] in Linear-sRGB
}
```

### WGSL Physical Shader Architecture (`vizdl_pbr.wgsl`)

The fragment shader evaluates first-principles radiance $L_o$:

```wgsl
struct MaterialUniform {
    linear_albedo: vec4<f32>,
    properties: vec4<f32>,     // x: roughness, y: metalness, z: transmission, w: ior
    emission: vec4<f32>,       // rgb: emission_color, a: excitation
};

// 1. Cook-Torrance Microfacet Specular BRDF
// D: Trowbridge-Reitz GGX Normal Distribution Function
// G: Smith Schlick-GGX Geometric Shadowing-Masking
// F: Schlick's approximation for Fresnel reflectance

// 2. Conductor Branch (metalness == 1.0)
// Zero diffuse refraction, pure Drude plasma specular reflection

// 3. Dielectric Branch (metalness == 0.0)
// Splitting energy: Ks = F, Kd = (1 - Ks) * (1 - transmission)
// Refracted ray transmits through medium obeying Beer-Lambert attenuation

// 4. Resonant Quantum Luminescence
// Direct radiant emission: L_emit = emission.rgb * (emission.a * EMISSION_INTENSITY_SCALE)
```

---

## 4. Headless Deterministic Verification

Because VizDL is designed around rigorous mathematical physics, its rendering output is **deterministic and testable**.

Implementations must support headless offscreen rendering (`wgpu::TextureUsages::COPY_SRC`) enabling continuous integration tests to render scenes into memory buffers and assert physical properties:
- Proving that an opaque dielectric (e.g. `glossy_red`) yields linear red channel dominance ($R_{\text{linear}} > 35 \times G_{\text{linear}}$).
- Proving that an excited material ($\epsilon > 0.0$) illuminates the camera buffer without requiring external lights.
- Proving that energy conservation ($R + T + A = 1.0$) holds within numerical epsilon across all angles of incidence.
