# Piperack Pedestal Foundation Design

Isolated pedestal footing under biaxial eccentric load, matching the methodology
of a Hyundai E&C piperack foundation calculation note, updated to **ACI 318-M14**
(IBC 2009 / ASCE 7-05). Single static `index.html` — open it directly, or host
on GitHub Pages.

## ACI 318-M14 vs the source report's ACI 318-11

The shear equations, β1, flexural design, minimum reinforcement, and bearing
provisions are unchanged between ACI 318-11 and ACI 318-M14 — the significant
shear-equation rewrite came later, in ACI 318-19. The one real difference this
app implements: the pedestal's strength-reduction factor **φ** now comes from
net tensile strain εt per ACI 318-M14 Table 21.2.2 (φ = 0.65 for εt ≤ εty,
0.90 for εt ≥ 0.005, linear between), solved iteratively since Pn depends on φ
and φ depends on the strain state at that Pn. The source report used an older
Pu/(0.1·f'c·Ag) linear approximation common in ACI 318-11-era hand-calc
templates; the two typically land within a few hundredths of each other.

## Default data

Load cases and combinations are pre-loaded with the actual project standard: 41 primary load
cases and 224 combinations (123 LRFD strength / ultimate + 101 ASD serviceability / elastic)
for Pipe Rack & Tech Structures, per 1-CF0000-07-BOD-1000-00 Rev B3, Appendices A.1.1 and
A.2.1. The combination factors are exact; load case reactions (Fx, Fy, Fz, Mx, My) are zeroed
as placeholders — enter your STAAD/analysis reactions per pedestal on the Load Cases tab before
reading any result.

## What's editable

- **Geometry** tab — footing, soil, pedestal, concrete & reinforcement.
- **Load cases** tab — define as many primary load cases as you need (dead,
  live, wind, seismic, friction, anchor, temperature...), each as reactions
  (Fx, Fy, Fz, Mx, My) at the top of the pedestal.
- **Combinations** tab — define as many load combinations as you need. Each
  row is a linear combination of the load cases above (enter the factor in
  each load case's column), tagged as `elastic` (service loads → bearing,
  stability, crack width) or `ultimate` (factored loads → footing/pedestal
  strength design). The "Soil & Fdn factor" column scales the automatically
  computed self-weight of the footing, the soil above it, and the pedestal.

## What's checked

- Load summary across all combinations
- Soil bearing pressure (4-corner biaxial), including the **tension case**
  using a digitized version of the report's C3 modification-factor table
  (bilinear interpolation, validated against the report's own worked examples)
- Overturning and sliding stability
- Footing one-way shear, two-way (punching) shear, and flexural reinforcement
  — governing load combination selected automatically per axis
- Crack width (ACI 350, Gergely-Lutz)
- Pedestal biaxial design by the Bresler load-contour method, with β solved
  exactly (root-finding), and Mnox/Mnoy computed by strain-compatibility
  instead of reading them off a P-M interaction chart

## Detailed calculation report

The **Results** tab shows a pass/fail summary. Click **"Generate detailed
calculation report →"** to build a full step-by-step narrative report (Report
tab) in the style of the source calc note — every formula shown with the
actual numbers substituted in, for every governing combination, through
soil bearing, stability, footing design, crack width, and pedestal design.
Any check that fails gets an extra "Why it fails" box explaining what
exceeded what, and by how much, with practical next steps. Use **Print /
Save as PDF** on that tab to export it (the print stylesheet hides the
input tabs and keeps just the report).

## Validation

The formulas were checked line-by-line against the source report's own worked
numbers (load combination totals, 4-corner bearing, C3 table lookups, one-way/
two-way shear capacity, flexural reinforcement, crack width) and match to
within normal rounding. The one exception is pedestal Mnox/Mnoy: those come
from strain-compatibility rather than the original hand-read chart, so expect
roughly 15–25% deviation from a chart-based calculation of the same section —
cross-check with column design software before using for a stamped submission.

## Scope not covered

- Multiple pedestals sharing one footing (the source report supports this;
  this tool models one pedestal, optionally offset from the footing center)
- Rectangular (non-uniform) footing reinforcement layout beyond a single
  governing bar size

## Publishing on GitHub Pages

```
git init
git add .
git commit -m "Initial commit: piperack pedestal foundation design app"
git branch -M main
git remote add origin https://github.com/<you>/piperack-fdn.git
git push -u origin main
```
Then **Settings → Pages → Deploy from branch (main, root)**.
