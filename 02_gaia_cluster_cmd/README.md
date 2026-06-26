# Pipeline 02 — Gaia DR3 Open Cluster Color-Magnitude Diagram

## Objective
Query the ESA Gaia DR3 billion-source catalog, isolate true members 
of the Hyades open cluster using astrometric filtering, and produce 
a publication-quality Color-Magnitude Diagram revealing the cluster's 
stellar populations and evolutionary features.

## Target
**Hyades Open Cluster** — the closest open cluster to Earth at 46.75 
parsecs (153 light years), located in the constellation Taurus. Age 
approximately 625 million years, containing ~400 confirmed members.

## Hardware
- **Machine:** Lenovo G50-80
- **CPU:** Intel Core i3-5005U (2.0 GHz, 4 logical cores, no turbo)
- **RAM:** 8 GB DDR3
- **Storage:** 1 TB HDD
- **OS:** Ubuntu 23.10, Linux 6.5.0

## Data Source
ESA Gaia Data Release 3 (DR3) — queried via ADQL through astroquery.
No local data files required — all data fetched from the Gaia archive
server-side, with only the filtered subset (~1,133 stars) downloaded.

## Pipeline Stages

### 1. ADQL Archive Query
- Cone search: 10° radius around RA=66.5°, Dec=+15.87°
- Server-side filters: parallax 15–30 mas, parallax SNR > 10
- Returns: 1,133 candidate stars from 1.8 billion source catalog

### 2. Proper Motion Filtering
- Hyades proper motion: pmra=+105, pmdec=-28 mas/yr
- Tolerance: 20 mas/yr in quadrature
- Eliminates field stars at same distance but unrelated kinematics
- Retained: 319 stars

### 3. Parallax Refinement
- Tighter cut: 18–26 mas (38–56 pc)
- Centred on Hyades parallax of 21.4 mas
- Retained: 301 confirmed members

### 4. Distance Calculation
- Per-star distances: d(pc) = 1000/parallax(mas)
- Per-star distance modulus: μ = 5×log10(d) − 5
- Individual corrections applied — not cluster mean

### 5. Absolute Magnitude Calculation
- M_G = G − distance_modulus
- Normalises all stars to intrinsic luminosity at 10 pc

### 6. Color-Magnitude Diagram
- X-axis: BP-RP colour index
- Y-axis: Absolute G magnitude (inverted — bright at top)
- Coloured by stellar temperature
- Annotated: main sequence, turn-off, giants, Sun reference

## Results

| Parameter | Measured | Literature | Accuracy |
|---|---|---|---|
| Distance | 47.2 pc | 46.75 pc | 0.96% error |
| Proper motion RA | 104.52 mas/yr | 105.0 mas/yr | 0.46% error |
| Proper motion Dec | -27.11 mas/yr | -28.0 mas/yr | 3.18% error |
| Members confirmed | 301 | ~300-400 | Within range |

## CMD Features Identified
- Main sequence spanning M_G = 2.5 to 17.1
- Main sequence turn-off at BP-RP ≈ 0.3 (A/F type, ~2 solar masses)
- Red giant branch (upper right, evolved stars)
- Sun-like G stars at M_G ≈ 4.6, BP-RP ≈ 0.65
- M dwarf sequence extending to M_G ≈ 17

## Output Files
- `hyades_proper_motion.png` — vector point diagram (2 panels)
- `hyades_cmd.png` — color-magnitude diagram (2 panels)
- `hyades_diagnostics.png` — member selection diagnostics (3 panels)

## Dependencies
```text
astropy==8.0.0
astroquery==0.4.x
numpy==2.4.6
matplotlib==3.11.0
```

## Key Engineering Decisions
- Server-side ADQL filtering — only 1,133 rows downloaded from 
  1.8 billion source catalog. No local storage of full catalog.
- Per-star distance correction rather than cluster mean — tightens
  the main sequence by correcting for real physical cluster depth.
- Two-stage membership filter — proper motion then parallax — 
  mirrors the methodology used in professional membership studies.
- Percentile-based colour normalisation — prevents outliers from
  washing out the colour gradient across the CMD.

## What This Demonstrates
- ADQL query construction for billion-row astronomical databases
- Multi-parameter astrometric membership determination
- Distance modulus calculation and absolute magnitude conversion
- Identification of stellar evolutionary features in observational data
- Publication-quality scientific visualisation with physical annotation
