# Materials in VizDL

Materials define passive matter and self-radiant active matter from first-principles condensed-matter physics.

---

## 1. The Condensed-Matter Triad

In VizDL, materials are defined by physical condensed-matter parameters rather than RGB surface colors. All material properties are normalized to the unit interval `[0.0, 1.0]`:

```toml
name = "<string>"                 # Required. Unique material identifier.
description = "<string>"          # Optional.
energy_gap = <f64>                # Required. Range: [0.0, 1.0]. Normalized bandgap energy.
electron_density = <f64>          # Required. Range: [0.0, 1.0]. Normalized electron density.
roughness = <f64>                 # Required. Range: [0.0, 1.0]. Normalized microfacet roughness.
excitation = <f64>                # Optional. Default: 0.0. Range: [0.0, 1.0]. Normalized electronic excitation level.
```

---

## 2. Parameter Physics and Regimes

### 2.1 Bandgap Energy ($E_g \in [0.0, 1.0]$)

The bandgap energy defines the threshold required to excite electrons into the conduction band:

- **Conductors / Metals ($E_g \le 0.05$)**:
  Zero or negligible bandgap. Free electrons form a Fermi-Dirac plasma sea:
  - Transmission is strictly zero ($T = 0.0$).
  - Surface reflectance follows Drude metallic plasma reflection ($R \approx 0.85 - 0.98$).
- **Dielectrics and Semiconductors ($E_g > 0.05$)**:
  Electrons are bound. Photons with energy below $E_g$ cannot be absorbed:
  - Wide bandgap ($E_g \ge 0.90$): Visible photons pass unabsorbed (diamond, quartz, clear glass, transparent resin).
  - Narrow bandgap ($0.05 < E_g < 0.90$): Selective absorption of high-energy visible photons (blue/green), allowing lower-energy photons (red/amber) to scatter or transmit, yielding rich physical colors.

### 2.2 Electron Density ($\rho_e \in [0.0, 1.0]$)

Governs refractive index $n$ according to the Lorentz-Lorenz relation:
$$n = \sqrt{1 + 1.42 \rho_e}$$

- **Transmissive Medium ($\rho_e \le 0.40$)**:
  Low valence electron density produces low internal scattering. Materials act as transmissive optical bodies (glasses, crystals, clear polymers).
- **Bulk Scattering Opaque Medium ($\rho_e > 0.40$)**:
  High electron density creates multiple internal scattering, extinguishing direct transmission ($T = 0.0, \text{Opacity} = 1.0$) as seen in ceramics, metals, stones, and dense polymers.

### 2.3 Microfacet Roughness ($\sigma \in [0.0, 1.0]$)

Represents the root-mean-square slope variance $\alpha_{\text{GGX}}$ of the surface microfacets:
- $\sigma = 0.0$: Optically smooth mirror specular reflection.
- $\sigma = 1.0$: Fully diffuse Lambertian scatterer.

### 2.4 Resonant Excitation ($\epsilon \in [0.0, 1.0]$)

Defines non-thermal electronic pumping. When $\epsilon > 0.0$, the material radiates resonant luminescence at its characteristic bandgap wavelength:
$$\Phi_{\text{emit}} = \epsilon \cdot L_{\max}$$

---

## 3. Physical Material Reference Library

### Polished Metal (Mirror Conductor)
```toml
name = "polished_metal"
energy_gap = 0.01        # Conductor free-electron plasma
electron_density = 0.92  # High plasma frequency, maximum reflectance
roughness = 0.05         # Mirror-like specular polish
```

### Pure Optical Glass (Dielectric Transparent)
```toml
name = "clear_glass"
energy_gap = 0.95        # Wide bandgap (unabsorbed visible spectrum)
electron_density = 0.36  # Refractive index n ~ 1.52
roughness = 0.02         # Smooth optical surface
```

### Aerodynamic Glossy Red (Pigmented Dielectric)
```toml
name = "glossy_red"
energy_gap = 0.22        # Absorbs green/blue; reflects pure crimson red
electron_density = 0.65  # Opaque bulk scattering (transmission = 0.0)
roughness = 0.15         # Clearcoat gloss
```

### Resonant Quantum Luminary (Self-Illuminating Active Matter)
```toml
name = "radiant_beacon"
energy_gap = 0.85
electron_density = 0.35
roughness = 0.10
excitation = 0.90        # High electronic pump driving physical glow
```
