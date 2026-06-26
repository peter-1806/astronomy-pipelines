# Pipeline 01 — TESS Exoplanet Transit Detection

## Objective
Detect exoplanet transits in real NASA TESS space telescope data using a 
fully local, memory-efficient pipeline built on constrained consumer hardware.

## Target
**TOI-270** — an M-dwarf star hosting 3 confirmed exoplanets located 
73 light years from Earth. This system was selected because it has 
multiple confirmed planets with well-characterised orbital parameters, 
allowing quantitative validation of the pipeline's detection accuracy.

## Hardware
- **Machine:** Lenovo G50-80
- **CPU:** Intel Core i3-5005U (2.0 GHz, 4 logical cores, no turbo)
- **RAM:** 8 GB DDR3
- **Storage:** 1 TB HDD
- **OS:** Ubuntu 23.10, Linux 6.5.0

## Pipeline Steps

### 1. Data Acquisition
- Queried NASA MAST archive via `lightkurve`
- Downloaded SPOC 2-minute cadence light curve for Sector 1
- Data cached locally at `~/.lightkurve-cache` to avoid re-downloading
- Raw dataset: 19,412 cadences spanning ~18 days

### 2. Quality Masking
- SPOC quality bitmask (17087) applied automatically on download
- Removed 5,871 flagged cadences (30%) caused by cosmic rays,
  spacecraft momentum dumps, and scattered earthlight
- Retained: 13,541 science-quality data points

### 3. Detrending
- Applied Savitzky-Golay filter with window length of 401 cadences
  (~13.4 hours) to remove slow instrumental and stellar trends
- Window length chosen to preserve transit signals (duration ~1.2 hours)
  while removing longer-timescale systematics
- Flux normalised to median = 1.0 for dimensionless comparison

### 4. Outlier Removal
- Sigma clipping at 4σ threshold applied to flattened light curve
- Removed 560 outlier points (4.1% of data)
- Threshold of 4σ chosen to preserve genuine transit dips
  while removing instrumental spikes

### 5. BLS Transit Search
- Box Least Squares algorithm via `astropy.timeseries.BoxLeastSquares`
- Period search range: 0.5 to 15.0 days
- Transit duration grid: 0.05, 0.1, 0.2 days
- Frequency factor: 1.0

### 6. Phase Folding
- Light curve folded at detected period
- Individual measurements binned at 5-minute resolution
- Transit shape recovered cleanly at phase = 0.0

## Results

| Parameter | Detected | Known (literature) | Error |
|---|---|---|---|
| Orbital period | 5.6638 days | 5.6600 days | 0.067% |
| Transit duration | 1.20 hours | ~1.3 hours | ~8% |
| Planet | TOI-270c | TOI-270c | ✓ confirmed |

## Output Files
- `toi270_raw.png` — raw light curve as downloaded
- `toi270_flatten.png` — 3-panel detrending diagnostic
- `toi270_clean.png` — cleaned and normalised light curve
- `toi270_transit.png` — phase-folded transit detection

## Dependencies
```text
astropy==8.0.0
lightkurve==2.6.0
numpy==2.4.6
matplotlib==3.11.0
```

## Key Engineering Decisions for Constrained Hardware
- Single sector processed at a time to keep RAM under 500 MB
- Local caching prevents repeated archive downloads
- Data processed sequentially, not in parallel, to suit single HDD
- No GPU acceleration required — full pipeline runs on CPU only
- Total RAM usage during pipeline: < 400 MB

## What This Demonstrates
- End-to-end time series astronomy pipeline from raw data to detection
- Correct application of quality masking, detrending, and sigma clipping
- BLS period recovery to 0.067% accuracy on real space telescope data
- Memory-efficient pipeline design suitable for constrained environments
- Reproducible, documented, version-controlled scientific workflow
