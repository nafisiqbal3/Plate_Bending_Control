# Simulations

MATLAB implementation of the plate-bending process model used to simulate the bending process before/independently of the real-time experiments. This is the simulation counterpart to the C++ code in `real_time_codes/` — it implements the same underlying geometric and moment-curvature model, but runs offline over a full range of punch displacements and produces plots rather than driving hardware.

## Contents

| File | Description |
|---|---|
| `Bending_Model.m` | Self-contained script that simulates the full bending process (geometry, force, springback, and process/controller gain) over a range of ram displacements and plots the results. |

## What it does

Given material properties, tooling geometry, and a punch-displacement range, the script:

1. **Geometry** — computes the wrap arc length `S1` and loaded bend angle `theta_1` at each displacement, handling the pre-contact (phase 1) and post-contact/die-wrap (phase 2) regions and matching them at the phase-transition displacement.
2. **Moment-curvature model** — builds a 3-region (elastic → elastic-plastic → plastic/die-conforming) moment-curvature relationship from the material's yield stress, plastic modulus, and die/tool geometry, solving for the region-2 blending constants (`A`, `B`, `C`) via `solveABC_region2`.
3. **Clamped-beam displacement model** — for each punch displacement, numerically inverts the moment-curvature relationship (via `fzero`, falling back to grid search) to find the bending moment and curvature consistent with a clamped-beam deflection integral, giving the bending force and curvature at that displacement.
4. **Springback** — computes the unloaded bend angle (`theta_2 = theta_1 - Springback`) from the clamped moment and arc length.
5. **Process/controller gain** — differentiates the unloaded angle with respect to displacement to get the process gain (deg/mm) and its inverse, the controller gain (mm/deg).

## Outputs

Running the script (no inputs required) produces six figures:

- Arc length (`S1`) vs. punch displacement
- Loaded bend angle (`theta_1`) vs. punch displacement
- Unloaded bend angle (`theta_2`) vs. punch displacement, with elastic and elasto-plastic limits marked
- Bending force vs. punch displacement
- Process gain (deg/mm) vs. punch displacement
- Controller gain (mm/deg) vs. punch displacement

It also prints phase-transition and elastic/elasto-plastic limit values to the console.

## Usage

Open `Bending_Model.m` in MATLAB and run it directly — all parameters (elastic modulus, yield stress, plastic modulus, die/tool radii, plate thickness/width, displacement range, etc.) are set at the top of the script and can be edited there to match your material and tooling.

## Prerequisites

- MATLAB (no toolboxes beyond base MATLAB — uses `fzero` and `trapz`)

## Reference

See the top-level [README](../README.md) and the associated paper for the full derivation of the process model and how this simulation relates to the real-time control code in `real_time_codes/`.
