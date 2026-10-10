# Materials in VizDL

Materials define passive matter and self-radiant active matter from first-principles condensed-matter physics.

---

## 1. The Condensed-Matter Triad and Physical Configuration

In VizDL, materials are defined by foundational condensed-matter properties rather than arbitrary RGB surface colors.
Properties represent physical quantities. In `materials/*.toml`, properties can be declared either:
- **Normalized** as dimensionless values in $[0.0, 1.0]$.
- **Physical** as raw physical quantities ($x > 1.0$), which implementations normalize according to the domain bounds configured in `visual.toml`.

```toml
name = "<string>"                 # Required. Unique material identifier.
description = "<string>"          # Optional.
energy_gap = <f64>                # Required. Bandgap energy (normalized [0.0, 1.0] or physical eV).
electron_density = <f64>          # Required. Conduction/valence density (normalized [0.0, 1.0] or physical 10^28 m^-3).
roughness = <f64>                 # Required. Microfacet RMS slope alpha in [0.0, 1.0].
excitation = <f64>                # Optional. Default: 0.0. Fractional electronic pumping in [0.0, 1.0].
```

---

## 2. Physical Scale Boundaries (`visual.toml`)

The boundaries of the physical universe are defined in `visual.toml` at the workspace root:

$$u = \frac{x - \min}{\max - \min} \iff x = \min + u \cdot (\max - \min)$$

Implementations provide clearly labelled code-level defaults. When `visual.toml` is present, its values strictly override these defaults.

The standard defaults are clean, memorable values:

| Property | Default Min | Default Max | Physical Unit | Physical Scale Meaning |
| :--- | :--- | :--- | :--- | :--- |
| `energy_gap` | `0.0` | `10.0` | $\text{eV}$ | Metals ($0.0\text{ eV}$) to deep-UV optical insulators ($10.0\text{ eV}$) |
| `electron_density` | `0.0` | `20.0` | $10^{28}\text{ m}^{-3}$ | Atomic packing scale ($\sim 10^{28}\text{ atoms/m}^3$): vacuum ($0$) to dense conductors (Al at $18.1$) |
| `roughness` | `0.0` | `1.0` | dimensionless | Microfacet RMS slope $\alpha$: specular mirror ($0.0$) to Lambertian diffuse ($1.0$) |
| `excitation` | `0.0` | `1.0` | dimensionless | Fractional population inversion: ground state ($0.0$) to inversion ($1.0$) |

### Example `visual.toml`
```toml
name = "VisualWorld"
description = "Global physical scale boundaries for condensed-matter properties"

[properties.energy_gap]
min = 0.0
max = 10.0

[properties.electron_density]
min = 0.0
max = 20.0

[properties.roughness]
min = 0.0
max = 1.0

[properties.excitation]
min = 0.0
max = 1.0
```

---

## 3. Parameter Physics and Regimes

### 3.1 Bandgap Energy ($E_g$)
The bandgap energy defines the threshold required to excite electrons into the conduction band:

- **Conductors / Metals ($E_g \le 0.05$ or $\le 0.5\text{ eV}$)**:
  Zero or negligible bandgap. Free electrons form a Fermi-Dirac plasma sea:
  - Transmission is strictly zero ($T = 0.0$).
  - Surface reflectance follows Drude metallic plasma reflection ($R \approx 0.85 - 0.98$).
- **Dielectrics and Semiconductors ($E_g > 0.05$ or $> 0.5\text{ eV}$)**:
  Electrons are bound. Photons with energy below $E_g$ cannot be absorbed:
  - Wide bandgap ($E_g \ge 0.90$ or $\ge 9.0\text{ eV}$): Visible photons pass unabsorbed (diamond, quartz, clear glass, transparent resin).
  - Narrow bandgap ($0.05 < E_g < 0.90$): Selective absorption of high-energy visible photons (blue/green), allowing lower-energy photons (red/amber) to scatter or transmit, yielding rich physical colors.

### 3.2 Electron Density ($\rho_e$)
Governs refractive index $n$ according to the Lorentz-Lorenz relation:
$$n = \sqrt{1 + 1.42 \rho_e}$$

- **Transmissive Medium ($\rho_e \le 0.40$ or $\le 8.0 \times 10^{28}\text{ m}^{-3}$)**:
  Low valence electron density produces low internal scattering. Materials act as transmissive optical bodies (glasses, crystals, clear polymers).
- **Bulk Scattering Opaque Medium ($\rho_e > 0.40$ or $> 8.0 \times 10^{28}\text{ m}^{-3}$)**:
  High electron density creates multiple internal scattering, extinguishing direct transmission ($T = 0.0, \text{Opacity} = 1.0$) as seen in ceramics, metals, stones, and dense polymers.

### 3.3 Microfacet Roughness ($\sigma \in [0.0, 1.0]$)
Represents the root-mean-square slope variance $\alpha_{\text{GGX}}$ of the surface microfacets:
- $\sigma = 0.0$: Optically smooth mirror specular reflection.
- $\sigma = 1.0$: Fully diffuse Lambertian scatterer.

### 3.4 Resonant Excitation ($\epsilon \in [0.0, 1.0]$)
Defines non-thermal electronic pumping. When $\epsilon > 0.0$, the material radiates resonant luminescence at its characteristic bandgap wavelength:
$$\Phi_{\text{emit}} = \epsilon \cdot L_{\max}$$

---

## 4. Physical Material Reference Library

### Polished Metal (Mirror Conductor)
```toml
name = "polished_metal"
energy_gap = 0.01        # Conductor free-electron plasma (approx 0.1 eV)
electron_density = 0.92  # High plasma frequency, maximum reflectance (approx 18.4 x 10^28 m^-3)
roughness = 0.05         # Mirror-like specular polish
```

### Pure Optical Glass (Dielectric Transparent)
```toml
name = "clear_glass"
energy_gap = 0.95        # Wide bandgap (approx 9.5 eV, unabsorbed visible spectrum)
electron_density = 0.36  # Refractive index n ~ 1.52 (approx 7.2 x 10^28 m^-3)
roughness = 0.02         # Smooth optical surface
```

### Aerodynamic Glossy Red (Pigmented Dielectric)
```toml
name = "glossy_red"
energy_gap = 0.22        # Absorbs green/blue (approx 2.2 eV); reflects pure crimson red
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
