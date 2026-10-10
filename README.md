# Instructional Radio Interferometer

An instructional radio interferometer developed at IIT Indore for observations near the 21-cm neutral-hydrogen line (1.420 GHz). This repository brings together the antenna and RF front end, SDR acquisition, synchronisation, correlation, calibration, and observing analysis.

The MSc thesis established two-element solar fringe detection and four-element/six-baseline bench coherence tests. Ongoing work extends the system with an updated GNU Radio pipeline, new analysis tools, solar observations, Galactic Centre observations and calibration, and four-element on-sky solar validation. Thesis results and later work are documented separately.

## Start here

- [Synchronisation](docs/synchronization.md)
- [System architecture](docs/system-architecture.md)
- [Observations and data products](docs/observations.md)
- [Reproducibility notes](docs/reproducibility.md)
- [Hardware](hardware/) · [GNU Radio](gnuradio/) · [Python analysis](python/) · [Results](results/) · [Images](images/)

## System overview

DIY pyramidal horn antennas feed low-noise amplified and filtered RF chains. SDRs digitise the signals, and an FX-style GNU Radio pipeline computes complex cross-correlations for phase and visibility analysis.

| Setup | Work documented |
|---|---|
| One USRP B210, two channels | Two-element solar fringe observations |
| Two USRP B210s, four channels | Four-element/six-baseline bench coherence tests using shared 10 MHz, 1 PPS, and PPS-aligned timed starts |
| Four-element on-sky setup | Solar validation; observation details and analysis are being added |
| RFSoC ZCU216 | Backend/correlator development |

Bench coherence tests and astronomical fringe detections are different validation steps; one should not be presented as proof of the other.

## Synchronisation

A shared frequency reference alone does not guarantee repeatable phase alignment between independent SDRs. The documented two-B210 setup uses a common **10 MHz reference**, **1 PPS**, and a **PPS-aligned timed start through UHD**. The [synchronisation guide](docs/synchronization.md) covers the setup and checks.

## Reproducing the work

For each observation or analysis, record hardware and channel mapping, software versions, observing parameters, input format, calibration steps, and the command or script used to make the result. Include small example data where possible; for large raw files, provide metadata and checksums instead of committing multi-gigabyte datasets.

## Repository layout

- `hardware/` — antenna and RF chain
- `gnuradio/` — flowgraphs and acquisition code
- `python/` — analysis and plotting
- `docs/` — setup, synchronisation, and observing notes
- `images/` — project photographs and figures
- `results/` — example plots and products

The thesis *Design and Development of an Instructional Interferometer for Radio Observations* (IIT Indore, 2026) documents the initial system and bench tests. This repository is being extended with later observations and processing work; details will be added as they are prepared.
