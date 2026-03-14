# Curso_hysplit — Jupyter Notebooks for HYSPLIT Trajectory Analysis

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Language: Python](https://img.shields.io/badge/Language-Python%203-blue.svg)]()
[![Jupyter](https://img.shields.io/badge/Interface-Jupyter%20Notebook-orange.svg)](https://jupyter.org/)
[![HYSPLIT](https://img.shields.io/badge/Model-HYSPLIT%20v5.2-lightblue.svg)](https://www.arl.noaa.gov/hysplit/)
[![PySPLIT](https://img.shields.io/badge/Library-PySPLIT-green.svg)](https://github.com/mscross/pysplit)

> A collection of **Jupyter notebooks** for running back-trajectory analyses, frequency maps, cluster analyses, and multi-site/multi-time trajectory ensembles with **HYSPLIT v5.2** installed locally on the same machine. The notebooks use the [PySPLIT](https://github.com/mscross/pysplit) Python package as the interface between Python and the HYSPLIT executable, enabling reproducible, publication-quality workflows entirely within a Jupyter environment.

---

## Table of Contents

- [What is HYSPLIT?](#what-is-hysplit)
- [Requirements](#requirements)
- [Installation](#installation)
  - [1. Install HYSPLIT v5.2](#1-install-hysplit-v52)
  - [2. Set up the Python environment](#2-set-up-the-python-environment)
    - [2a. Install Miniconda](#2a-install-miniconda-if-not-already-installed)
    - [2b. Create and activate the environment](#2b-create-and-activate-the-environment)
    - [2c. Install scientific dependencies](#2c-install-scientific-dependencies)
    - [2d. Install JupyterLab](#2d-install-jupyterlab)
    - [2e. Install PySPLIT](#2e-install-pysplit)
    - [2f. Register the environment as a Jupyter kernel](#2f-register-the-environment-as-a-jupyter-kernel)
    - [2g. Launch JupyterLab](#2g-launch-jupyterlab)
  - [3. Download meteorological data](#3-download-meteorological-data)
  - [4. Clone this repository](#4-clone-this-repository)
- [Repository Contents](#repository-contents)
- [Notebook Descriptions](#notebook-descriptions)
  - [Trayect_hysplit.ipynb — Single trajectory](#trayect_hysplitipynb--single-trajectory)
  - [Trayect_MTimes_hysplit.ipynb — Multiple start times](#trayect_mtimes_hysplitipynb--multiple-start-times)
  - [Trayect_MTimes_hysplit_anual.ipynb — Annual trajectory ensemble](#trayect_mtimes_hysplit_anualipynb--annual-trajectory-ensemble)
  - [Trayect_MSites_hysplit.ipynb — Multiple receptor sites](#trayect_msites_hysplitipynb--multiple-receptor-sites)
  - [Trayect_MTS_hysplit.ipynb — Multiple times and sites combined](#trayect_mts_hysplitipynb--multiple-times-and-sites-combined)
  - [Frecuency_hysplit.ipynb — Trajectory frequency map](#frecuency_hysplitipynb--trajectory-frequency-map)
  - [Cluster_hysplit.ipynb — Trajectory cluster analysis](#cluster_hysplitipynb--trajectory-cluster-analysis)
- [Supplementary Material](#supplementary-material)
- [Key Configuration Parameters](#key-configuration-parameters)
- [Meteorological Data](#meteorological-data)
- [References](#references)

---

## What is HYSPLIT?

**HYSPLIT** (HYbrid Single-Particle Lagrangian Integrated Trajectory) is an atmospheric transport and dispersion model developed by NOAA's Air Resources Laboratory (ARL). It computes the path of an air parcel — forward or **backward in time** — by integrating the 3D wind field from gridded meteorological analyses. Back-trajectory analysis is one of its most common applications: given a receptor location and time, HYSPLIT traces the most probable path that air arrived from, enabling the identification of pollution source regions.

Key capabilities used in these notebooks:

- **Back trajectories** — trace the origin of air masses arriving at a receptor site
- **Ensemble trajectories** — generate dozens to thousands of trajectories for different start times, heights, or sites
- **Cluster analysis** — group a large ensemble of trajectories by shape to identify the dominant transport regimes
- **Frequency / residence time maps** — compute how often trajectories pass through each grid cell, producing a spatial map of air mass origin probability

---

## Requirements

| Component | Version | Notes |
|---|---|---|
| HYSPLIT | v5.2 | Must be installed locally; desktop version from NOAA ARL |
| Python | ≥ 3.6 | Tested with 3.7–3.11 |
| PySPLIT | ≥ 0.3.5 | Python interface to HYSPLIT |
| Jupyter Notebook / JupyterLab | any | For running the notebooks |
| Matplotlib | ≥ 3.0 | Plotting |
| Cartopy or Basemap | any | Map projections and background maps |
| NumPy | any | Array operations |
| Pandas | any | Data handling |
| GeoPandas | any | Required by PySPLIT for trajectory data |

> **Important:** HYSPLIT v5.2 must be installed on the **same machine** where the notebooks are executed. The notebooks call the HYSPLIT executable (`hyts_std` or `hyts_std.exe`) directly through PySPLIT. The notebooks will not run on a remote server unless HYSPLIT is also installed there.

---

## Installation

### 1. Install HYSPLIT v5.2

Download and install the HYSPLIT desktop version for your operating system from the NOAA ARL website:

```
https://www.ready.noaa.gov/HYSPLIT.php
```

Note the installation path (e.g., `C:\hysplit` on Windows or `/opt/hysplit` on Linux/macOS) — you will need it when configuring PySPLIT.

### 2. Set up the Python environment

It is strongly recommended to use a **conda virtual environment** to avoid dependency conflicts, particularly with GeoPandas and Cartopy.

#### 2a. Install Miniconda (if not already installed)

Download the Miniconda installer for your operating system from:

```
https://docs.conda.io/en/latest/miniconda.html
```

| OS | Installer |
|---|---|
| Linux | `Miniconda3-latest-Linux-x86_64.sh` |
| macOS (Intel) | `Miniconda3-latest-MacOSX-x86_64.sh` |
| macOS (Apple Silicon) | `Miniconda3-latest-MacOSX-arm64.sh` |
| Windows | `Miniconda3-latest-Windows-x86_64.exe` |

Run the installer and follow the prompts. After installation, open a terminal (Linux/macOS) or **Anaconda Prompt** (Windows) and verify:

```bash
conda --version
```

#### 2b. Create and activate the environment

```bash
# Create a dedicated environment for HYSPLIT analysis
conda create --name hysplit_env python=3.9
conda activate hysplit_env
```

#### 2c. Install scientific dependencies

```bash
# Add conda-forge channel (required for cartopy and geopandas)
conda config --add channels conda-forge
conda config --set channel_priority strict

# Install all required packages
conda install numpy matplotlib pandas geopandas cartopy shapely fiona
```

#### 2d. Install JupyterLab

[JupyterLab](https://jupyterlab.readthedocs.io/) is the recommended interface for running the notebooks. It provides a full IDE-like environment with a file browser, tab management, and an integrated terminal — all in the browser.

```bash
# Install JupyterLab
conda install jupyterlab
```

Alternatively, if you prefer the classic Jupyter Notebook interface:

```bash
# Classic Jupyter Notebook (optional — JupyterLab is preferred)
conda install notebook
```

> **JupyterLab vs Jupyter Notebook:** Both can open and run `.ipynb` files. JupyterLab is the actively developed successor to the classic Notebook interface and is recommended for new users. All notebooks in this repository are compatible with both.

#### 2e. Install PySPLIT

PySPLIT is not available on conda-forge and must be installed via pip inside the active environment:

```bash
pip install pysplit
```

Or install the latest development version directly from GitHub:

```bash
pip install git+https://github.com/mscross/pysplit.git
```

#### 2f. Register the environment as a Jupyter kernel

This step ensures that JupyterLab uses the `hysplit_env` environment (with all installed packages) when running the notebooks:

```bash
conda install ipykernel
python -m ipykernel install --user --name hysplit_env --display-name "Python (hysplit_env)"
```

#### 2g. Launch JupyterLab

Navigate to the repository directory and start JupyterLab:

```bash
cd Curso_hysplit
jupyter lab
```

JupyterLab will open automatically in your default web browser at `http://localhost:8888/lab`. If it does not open automatically, copy the URL shown in the terminal (including the token) and paste it into your browser.

In JupyterLab, select the kernel **"Python (hysplit_env)"** when opening any notebook for the first time (top-right kernel selector), to ensure all dependencies are available.

> **Launching on a remote server:** If JupyterLab is running on a remote Linux server (HPC cluster, cloud VM), use SSH port forwarding to access it from your local browser:
>
> ```bash
> # On the remote server — start JupyterLab without opening a browser
> jupyter lab --no-browser --port=8888
>
> # On your local machine — forward the remote port to localhost
> ssh -L 8888:localhost:8888 user@remote-server
> ```
>
> Then open `http://localhost:8888/lab` in your local browser and enter the token shown in the server terminal.

### 3. Download meteorological data

The notebooks require **GDAS (Global Data Assimilation System) 1-degree** ARL-format meteorological files. These are the most commonly used input data for HYSPLIT back-trajectory analysis. They are freely available from the NOAA ARL archive:

```
https://www.ready.noaa.gov/archives.php
```

GDAS1 files follow the naming convention `gdas1.MMMYY.w[1-5]` (e.g., `gdas1.jan22.w1` for the first week of January 2022). Download the files covering the time period you want to analyse and place them in a dedicated directory (e.g., `/data/gdas/`).

Other supported meteorological datasets include NARR (North American Regional Reanalysis) and ERA5 in ARL format.

### 4. Clone this repository

```bash
git clone https://github.com/JoseAgustin/Curso_hysplit.git
cd Curso_hysplit
jupyter lab
```

---

## Repository Contents

```
Curso_hysplit/
├── Trayect_hysplit.ipynb              # Single back trajectory from one site and time
├── Trayect_MTimes_hysplit.ipynb       # Back trajectories for multiple start times
├── Trayect_MTimes_hysplit_anual.ipynb # Annual trajectory ensemble (all days in a year)
├── Trayect_MSites_hysplit.ipynb       # Back trajectories for multiple receptor sites
├── Trayect_MTS_hysplit.ipynb          # Combined multi-time and multi-site ensemble
├── Frecuency_hysplit.ipynb            # Trajectory frequency / residence time map
├── Cluster_hysplit.ipynb              # Trajectory cluster analysis
├── Dispersion_teoria.pdf              # Theoretical background: atmospheric dispersion
└── LICENSE
```

---

## Notebook Descriptions

The notebooks are ordered from simplest to most complex and are designed to be worked through in sequence as a course. Each notebook builds on the concepts introduced in the previous one.

### Trayect_hysplit.ipynb — Single trajectory

**Purpose:** Introductory notebook. Computes and visualises a single HYSPLIT back trajectory from one receptor location, one starting height, and one date/time.

**Covers:**
- Configuring PySPLIT: setting the path to the HYSPLIT working directory, executable, and meteorological files
- Defining the receptor site (latitude, longitude, altitude above ground level)
- Setting the trajectory duration (typically 72–120 hours backward)
- Running `pysplit.generate_trajectories()` to call the HYSPLIT executable
- Loading the output trajectory file and inspecting along-trajectory meteorological variables (pressure, temperature, specific humidity, mixed layer height, etc.)
- Plotting the trajectory on a map with Cartopy/Basemap

**Output:** A single trajectory path plotted on a regional or global map.

---

### Trayect_MTimes_hysplit.ipynb — Multiple start times

**Purpose:** Generates an ensemble of back trajectories from a fixed receptor site and height for multiple start times (e.g., every day at the same hour for a month).

**Covers:**
- Automating trajectory generation over a range of dates using `pysplit.generate_trajectories()` with a date list
- Loading multiple trajectory files into a `TrajectoryGroup` object
- Plotting all trajectories simultaneously, optionally coloured by a meteorological variable or by month
- Computing basic statistics over the ensemble

**Output:** A map showing the spread of air mass origin paths over the selected time period.

---

### Trayect_MTimes_hysplit_anual.ipynb — Annual trajectory ensemble

**Purpose:** Extends the multi-time workflow to a full calendar year, generating one trajectory per day (or per selected hour) for all 12 months.

**Covers:**
- Generating and managing large trajectory ensembles (up to 365 × N trajectories)
- Seasonal subsetting: splitting the `TrajectoryGroup` by meteorological season (DJF, MAM, JJA, SON) or by month
- Side-by-side seasonal trajectory maps
- Interpreting inter-seasonal variability in air mass origin

**Output:** Seasonal maps of back trajectories covering a full year, suitable for identifying dominant transport regimes at the receptor site.

---

### Trayect_MSites_hysplit.ipynb — Multiple receptor sites

**Purpose:** Computes back trajectories simultaneously for several receptor sites (e.g., different monitoring stations or urban areas) at the same date/time.

**Covers:**
- Defining a list of receptor sites with their respective coordinates and altitudes
- Running trajectory generation in a loop over the site list
- Organising results by site and overlaying trajectories from all sites on the same map
- Comparing air mass origins across different receptor locations

**Output:** A single map showing trajectories from multiple sites, colour-coded by receptor.

---

### Trayect_MTS_hysplit.ipynb — Multiple times and sites combined

**Purpose:** Combines the multi-time and multi-site approaches to generate a full trajectory matrix: N dates × M sites.

**Covers:**
- Nested loops over dates and sites
- Efficient file naming conventions to avoid output file collisions
- Building a structured data hierarchy: one `TrajectoryGroup` per site, loaded from all time steps
- Comparative visualisation of air mass climatology across multiple receptor sites

**Output:** Per-site trajectory ensemble maps for a user-defined period, enabling spatial comparison of pollution transport paths.

---

### Frecuency_hysplit.ipynb — Trajectory frequency map

**Purpose:** Converts a large trajectory ensemble into a gridded **trajectory frequency map** (also called a trajectory residence time map or source footprint), showing the probability that air passing through each grid cell contributed to concentrations at the receptor site.

**Covers:**
- Loading a `TrajectoryGroup` (typically from the annual or multi-time ensemble)
- Computing trajectory intersections with a regular lat/lon grid
- Normalising counts to obtain fractional frequency (%) per grid cell
- Plotting the frequency map as a filled contour or colour mesh map
- Interpreting frequency maxima as the most probable air mass origin sectors

**Output:** A gridded map showing the relative frequency (%) with which trajectories passed through each grid cell — a spatial diagnostic of dominant source regions.

---

### Cluster_hysplit.ipynb — Trajectory cluster analysis

**Purpose:** Groups a large trajectory ensemble into a small number of representative **cluster trajectories** using HYSPLIT's built-in spatial clustering algorithm, and plots the resulting cluster mean paths.

**Covers:**
- Preparing trajectory files for HYSPLIT clustering: clipping trajectories to path-only format using `pysplit.clip_traj()` to avoid multi-line timepoint issues
- Running HYSPLIT's cluster executable through PySPLIT to group trajectories by spatial similarity
- Loading the `ClusterGroup` output and examining each `Cluster` object
- Selecting the optimal number of clusters from the total spatial variance (TSV) elbow plot
- Plotting cluster mean trajectories with line thickness or size proportional to the number of member trajectories
- Associating clusters with measured pollutant concentrations or meteorological variables to identify which transport regime carries the highest pollution load

**Output:** A map of N cluster mean trajectories (typically 3–6 clusters), each representing a distinct synoptic transport pattern, with the percentage of trajectories belonging to each cluster.

---

## Supplementary Material

### Dispersion_teoria.pdf

A PDF document providing the theoretical background on atmospheric dispersion and Lagrangian trajectory modelling. Topics covered include:

- Fundamentals of Lagrangian particle tracking
- The HYSPLIT hybrid Lagrangian–Eulerian calculation method
- Atmospheric dispersion parameterisations (turbulent diffusion, convective mixing)
- Sources of trajectory uncertainty (meteorological analysis errors, sub-grid turbulence)
- Interpretation of back trajectories for source attribution
- Overview of available meteorological datasets (GDAS, NARR, ERA5) and their spatial/temporal resolution

This document is intended as the lecture notes accompanying the practical exercises in the notebooks.

---

## Key Configuration Parameters

Each notebook contains a configuration block near the top where the user sets the parameters for their specific application. The key parameters are:

| Parameter | Example value | Description |
|---|---|---|
| `working_dir` | `'/opt/hysplit/working'` | HYSPLIT working directory (where output files are written) |
| `hysplit_path` | `'/opt/hysplit/exec'` | Path to the HYSPLIT executable directory |
| `met_dir` | `'/data/gdas/'` | Directory containing meteorological data files |
| `met_files` | `['gdas1.jan22.w1', ...]` | List of meteorological file names covering the analysis period |
| `receptor_lat` | `19.43` | Receptor site latitude (degrees N) |
| `receptor_lon` | `-99.13` | Receptor site longitude (degrees E; negative = west) |
| `receptor_alt` | `500` | Receptor altitude above ground level (m AGL) |
| `run_time` | `-120` | Trajectory duration in hours (negative = backward in time) |
| `start_date` | `datetime(2022, 1, 1)` | First trajectory start date |
| `end_date` | `datetime(2022, 12, 31)` | Last trajectory start date |
| `interval` | `24` | Interval between consecutive trajectories (hours) |
| `start_hour` | `18` | UTC hour at which each trajectory starts |
| `n_clusters` | `5` | Number of clusters for the cluster analysis |

> **Altitude convention:** HYSPLIT and PySPLIT accept receptor heights in metres **above ground level (AGL)** by default. Common choices for boundary-layer back-trajectory studies are 100 m, 500 m, and 1500 m AGL to sample the surface, mixed layer, and free troposphere respectively.

---

## Meteorological Data

The notebooks are designed to work with **GDAS1** (Global Data Assimilation System, 1° × 1° resolution, 3-hourly) data in NOAA ARL packed format. This dataset is available from 2005 onward and is the most commonly used dataset for HYSPLIT back-trajectory analysis.

| Dataset | Resolution | Period | Download |
|---|---|---|---|
| GDAS1 | 1° × 1°, 3-hourly | 2005–present | https://www.ready.noaa.gov/archives.php |
| NARR | ~32 km, 3-hourly | 1979–2019 | https://www.ready.noaa.gov/READYamet.php |
| ERA5 (ARL format) | 0.25° × 0.25°, 1-hourly | 1940–present | https://www.ready.noaa.gov/READYamet.php |
| GDAS0.5 | 0.5° × 0.5°, 3-hourly | 2005–present | https://www.ready.noaa.gov/archives.php |

For studies over Mexico and Central America, GDAS1 or GDAS0.5 are recommended. NARR provides higher spatial resolution but is only available over North America and ends in 2019.

---

## References

- Stein, A. F., Draxler, R. R., Rolph, G. D., Stunder, B. J. B., Cohen, M. D., & Ngan, F. (2015). NOAA's HYSPLIT atmospheric transport and dispersion modeling system. *Bulletin of the American Meteorological Society*, **96**(12), 2059–2077. https://doi.org/10.1175/BAMS-D-14-00110.1

- Draxler, R. R., & Hess, G. D. (1998). An overview of the HYSPLIT_4 modelling system for trajectories. *Australian Meteorological Magazine*, **47**(4), 295–308.

- Warner, M. S. C. (2018). Introduction to PySPLIT: A Python toolkit for NOAA ARL's HYSPLIT model. *Computing in Science & Engineering*, **20**(5), 47–62. https://doi.org/10.1109/MCSE.2018.05329815

- NOAA Air Resources Laboratory. HYSPLIT — Hybrid Single-Particle Lagrangian Integrated Trajectory model. https://www.arl.noaa.gov/hysplit/

- NOAA Air Resources Laboratory. HYSPLIT Tutorials. https://www.ready.noaa.gov/HYSPLIT_Tutorials.php

---

*README last updated: March 2026*
