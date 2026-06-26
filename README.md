# Astronomy Data Pipelines

A collection of locally hosted, end-to-end scientific data pipelines 
for observational astronomy. Every pipeline in this repository was 
built, tested, and validated on a single constrained consumer laptop 
using only open-source tools and publicly available astronomical data.

This project exists to demonstrate subject matter expertise in 
astronomical data handling, pipeline engineering, and scientific 
computing under real-world hardware constraints — the kind of 
constraints that exist on university compute nodes, small observatory 
servers, and resource-limited research environments.

---

## Hardware Environment

| Component | Specification |
|---|---|
| Machine | Lenovo G50-80 |
| CPU | Intel Core i3-5005U (2.0 GHz, 4 logical cores, no turbo) |
| RAM | 8 GB DDR3 |
| Storage | 1 TB HDD (~100 MB/s read speed) |
| GPU | Intel HD Graphics 5500 (integrated, no CUDA) |
| OS | Ubuntu 23.10, Linux 6.5.0-28 |
| Python | 3.11 via Miniconda |

### Why This Matters

Modern astronomy produces data at scales that challenge even 
well-resourced institutions. The Gaia DR3 catalog contains 1.8 billion 
sources. TESS generates gigabytes of time-series data per sector. 
LSST/Rubin will produce 15 TB per night. Working effectively with 
large astronomical datasets is not just about having powerful hardware 
— it requires knowing how to design pipelines that are memory-efficient, 
I/O-aware, and reproducible regardless of the compute environment.

Every pipeline in this repository uses explicit strategies to handle 
data that exceeds naive RAM limits:
- Memory-mapped FITS I/O via astropy
- Chunked out-of-core processing via Dask
- Local caching to minimise repeated archive downloads
- Sequential HDD-aware processing patterns
- Swap-backed memory management (12 GB swap, swappiness=10)

---

## Software Stack

| Package | Version | Purpose |
|---|---|---|
| astropy | 8.0.0 | FITS I/O, coordinates, units, WCS, BLS |
| lightkurve | 2.6.0 | TESS/Kepler light curve handling |
| astroquery | latest | Archive queries (MAST, Gaia, SDSS, VizieR) |
| photutils | latest | Source detection, aperture photometry |
| specutils | latest | Spectral data handling and analysis |
| dask | 2024.8.2 | Out-of-core chunked array processing |
| vaex | 4.19.0 | Out-of-core DataFrame for large catalogs |
| numpy | 2.4.6 | Array mathematics |
| scipy | 1.17.1 | Signal processing, statistics, optimisation |
| matplotlib | 3.11.0 | Publication-quality visualisation |
| scikit-learn | 1.9.0 | Machine learning for classification tasks |
| jupyterlab | latest | Interactive development environment |

---

## Pipelines

### 01 — TESS Exoplanet Transit Detection
**Domain:** Time-series photometry  
**Status:** Complete  
**Data source:** NASA MAST archive via lightkurve  
**Target:** TOI-270 (M-dwarf, 3 confirmed exoplanets, 73 ly)

**What it does:**
Takes raw 2-minute cadence TESS space telescope data through a complete 
reduction and analysis pipeline — quality masking, detrending, outlier 
removal, BLS period search, and phase-folded transit recovery.

**Key results:**

| Parameter | Detected | Known | Accuracy |
|---|---|---|---|
| Orbital period | 5.6638 days | 5.6600 days | 0.067% error |
| Transit duration | 1.20 hours | ~1.3 hours | ~8% error |
| Planet identified | TOI-270c | TOI-270c | Confirmed |

**Pipeline stages:**
1. Archive query and cached download (19,412 raw cadences)
2. SPOC quality bitmask application (30% flagged, 13,541 retained)
3. Savitzky-Golay detrending (window=401 cadences, ~13.4 hours)
4. Sigma clipping at 4σ (560 outliers removed, 12,981 retained)
5. BLS period search (0.5–15.0 day range, 3 duration grids)
6. Phase folding and binned transit recovery

**Memory usage:** Peak ~380 MB RAM during BLS search  
**Runtime:** ~4 minutes on i3-5005U

---

### 02 — Gaia DR3 Open Cluster Color-Magnitude Diagram
**Domain:** Catalog analysis, stellar populations  
**Status:** In Progress  
**Data source:** ESA Gaia DR3 archive via astroquery  
**Target:** Hyades open cluster (closest cluster to Earth, ~47 pc)

**What it does:**
Queries the Gaia DR3 billion-source catalog using ADQL, filters cluster 
members using parallax and proper motion cuts, cross-matches photometric 
bands, and produces a clean Color-Magnitude Diagram identifying the main 
sequence, giant branch, and main sequence turn-off point.

---

### 03 — CCD Photometric Reduction Pipeline
**Domain:** Optical imaging, calibration  
**Status:** Planned  
**Data source:** Public telescope archive (Las Cumbres / NOAO)

**What it does:**
Takes raw science frames through the full calibration chain — master 
bias construction, dark subtraction, flat-field correction, bad pixel 
masking, astrometric solution, and source catalog extraction via 
aperture photometry.

---

### 04 — SDSS Batch Spectral Analysis
**Domain:** Spectroscopy, galaxy physics  
**Status:** Planned  
**Data source:** SDSS DR17 via astroquery

**What it does:**
Batch processes galaxy spectra from the Sloan Digital Sky Survey — 
continuum normalisation, emission line identification, Gaussian line 
fitting, equivalent width measurement, and redshift determination across 
a catalog of targets.

---

## Repository Structure


---

## Key Engineering Principles Applied

**Memory efficiency:**
Large FITS files opened with memmap=True so array data is read from 
disk on demand rather than loaded entirely into RAM. Catalog queries 
streamed in chunks rather than fetched wholesale.

**I/O awareness:**
Pipeline designed around HDD sequential read patterns. Parallel file 
access avoided — spinning disk performs worse under random concurrent 
reads. Data converted to HDF5 after first load for faster subsequent 
access.

**Reproducibility:**
All data fetched from public archives with explicit version references. 
Random seeds fixed where applicable. Results deterministic across runs.

**Defensive logging:**
Every data modification step logs before/after counts. Quality mask 
statistics, outlier removal counts, and detection parameters all 
printed to output for full auditability.

**Caching:**
Archive downloads cached locally after first fetch. Pipeline reruns 
use cached data, avoiding unnecessary load on public servers and 
dramatically reducing runtime after initial download.

---

## Relevance to Research Environments

These pipelines directly demonstrate skills relevant to:
- Maintaining and extending existing observatory data reduction pipelines
- Processing archival data from major surveys (TESS, Gaia, SDSS, 2MASS)
- Adapting pipelines to run on modest institutional compute resources
- Documenting and reproducing scientific data workflows
- Collaborating on open-source astronomy software

---

## Contact

GitHub: [peter-1806](https://github.com/peter-1806)
