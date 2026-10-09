# Instructional Radio Interferometer

An open, hands-on radio astronomy instrument developed at the Indian Institute of Technology Indore. The project documents the path from antenna and RF-front-end design to synchronised SDR acquisition, FX correlation, calibration, and on-sky validation near the 21-cm neutral-hydrogen line (1.420 GHz).

The goal is not just to show that an interferometer works, but to make its hardware, timing, signal processing, data products, and analysis understandable and reproducible by other students and researchers.

> **Project status:** The MSc thesis established two-element solar fringe detection and four-element/six-baseline bench coherence tests. Work continued after the thesis, including an updated GNU Radio pipeline, new analysis software, additional solar observations, Galactic Centre observing and calibration work, and four-element on-sky solar validation. Post-thesis procedures, code, and results are being added as they are documented and checked.

## Start here

- [Synchronisation guide](docs/synchronization.md)
- [System architecture](docs/system-architecture.md)
- [Reproducibility checklist](docs/reproducibility.md)
- [CASPER toolflow and Python environment setup](docs/casper-toolflow-setup.md)
- [Observation log and data products](docs/observations.md)
- [Hardware documentation](hardware/)
- [GNU Radio flowgraphs](gnuradio/)
- [Python analysis](python/)
- [Figures and system photographs](images/)
- [Example results](results/)

## What the instrument does

- Receives radio signals with DIY pyramidal horn antennas near 1.420 GHz.
- Amplifies and filters the RF signal using characterised low-noise front-end chains.
- Digitises signals using SDR backends; the USRP B210 was used for the thesis two-element observations.
- Computes complex cross-correlations with an FX-style GNU Radio pipeline.
- Studies phase coherence and multi-device synchronisation using two USRP B210 units.
- Measures solar drift-scan fringes and compares them with Earth-rotation predictions.
- Extends the workflow to calibration, Galactic Centre observations, and a four-element system.

## System configurations and validation

| Configuration | Purpose | Validation status |
|---|---|---|
| Two channels of one USRP B210 | Two-element interferometry | Solar fringe detections reported in the thesis |
| Two USRP B210 devices, four receive channels | Four elements and six baselines | Bench coherence validated in the thesis using a common reference, PPS, and timed start |
| Four-element on-sky setup | Astronomical validation beyond the bench test | Later project milestone; observation details and reproducible products are to be documented |
| RFSoC ZCU216 | Explore a hardware-accelerated backend/correlator | Development and testing; results will be documented separately |

These configurations are related but not interchangeable. Successful injected-tone coherence tests do not by themselves demonstrate on-sky fringe detection.

## Synchronisation: a central design lesson

A common reference frequency alone does not guarantee repeatable phase alignment between independent SDRs. The thesis found that the RTL-SDR clock daisy-chain did not provide stable inter-device phase coherence. The two channels of one USRP B210 shared a local oscillator and were phase-coherent, while two independent B210s required a shared **10 MHz reference**, a common **1 PPS timing signal**, and a **PPS-aligned timed-start sequence controlled through UHD**.

The [synchronisation guide](docs/synchronization.md) explains the rationale, bench test, channel mapping, failure modes, and checks. The exact acquisition script and version-specific UHD/GNU Radio settings must be version-controlled before the workflow can be reproduced end to end.

## Reproducibility principles

Every workflow should include:

1. Exact hardware and channel mapping, cabling, reference-clock and PPS distribution.
2. Software versions, installation steps, and device configuration.
3. The actual GNU Radio Companion flowgraph (.grc) and exported Python script, where applicable.
4. A documented configuration file or command-line arguments rather than machine-specific paths.
5. Input-data format, sample rate, centre frequency, FFT/channelisation settings, integration, and timestamps.
6. Calibration steps, quality checks, known limitations, and expected outputs.
7. A small, shareable example dataset and the command needed to reproduce each example figure.

Large raw observations may be hosted separately if needed; provide checksums, metadata, and stable download instructions rather than committing multi-gigabyte files directly to Git.

## Thesis and continued development

The thesis, *Design and Development of an Instructional Interferometer for Radio Observations* (IIT Indore, May 2026), documents antenna and RF-front-end design, backend evolution from RTL-SDR to USRP B210, synchronisation experiments, the six-baseline bench correlator, and four solar drift-scan sessions.

The repository is being extended to cover work performed after the thesis. Thesis results and later results will be labelled separately, with observation logs and processing versions attached to each dataset.

## Repository layout

- **hardware/** — antenna design, RF chain, component list, measurements
- **gnuradio/** — versioned GRC flowgraphs and acquisition scripts
- **python/** — analysis, calibration, plotting, and validation tools
- **docs/** — setup, synchronisation, observing, calibration, and reproducibility guides
- **images/** — original system photographs, diagrams, and flowgraph screenshots
- **results/** — small example products, plots, and result summaries

## Contributing and citing

If you reproduce or extend the system, record the hardware revision, software versions, synchronisation method, observation metadata, and deviations from the documented procedure. Citation details and a recommended citation will be added alongside the thesis/publication metadata.
