# Reproducibility checklist

Use this checklist before calling any hardware test, calibration run, or astronomical observation reproducible. A README alone is not enough: the code, exact configuration, input data, and expected output must also be available.

## A. Hardware and wiring

- [ ] Antenna/front-end block diagram and photographs
- [ ] Horn dimensions and fabrication files or drawings
- [ ] RF component manufacturer and part number
- [ ] Cable types/lengths and connector adapters
- [ ] Gain settings and RF power/saturation precautions
- [ ] SDR model, serial number, channel mapping, and USB port
- [ ] Reference-clock and PPS source, splitter/distribution diagram, and cable mapping
- [ ] Antenna locations, orientation, baseline length/vector, and coordinate convention

## B. Software environment

- [ ] OS and version
- [ ] GNU Radio and GNU Radio Companion versions
- [ ] UHD version and device firmware information
- [ ] Python version and dependency file
- [ ] Exact GRC flowgraph and corresponding exported Python script
- [ ] Exact synchronisation-aware acquisition code
- [ ] Configuration file or CLI arguments; no hard-coded personal paths or network addresses
- [ ] Commands to install dependencies and launch the workflow

## C. Acquisition configuration

- [ ] Target and observing mode
- [ ] Start/end times with timezone and UTC equivalents
- [ ] Centre frequency and sample rate
- [ ] FFT size, window, channel spacing, averaging/integration, and saved time step
- [ ] RF gain and any AGC/DC-removal/windowing settings
- [ ] Clock source, time source, PPS alignment, and scheduled start procedure
- [ ] Channel and baseline definitions
- [ ] File format, array dtype, byte order, shape, and time/frequency axis definitions
- [ ] Handling of dropped samples, overflow warnings, interrupted runs, and incomplete frames

## D. Calibration and analysis

- [ ] Calibration method, calibrator/source, timestamp, and applied correction
- [ ] Explanation of visibility convention, including which channel is conjugated
- [ ] Units and definitions for every plotted quantity
- [ ] RFI/channel masking and flagged-data handling
- [ ] Script to reproduce every headline plot from named input files
- [ ] Script output logs and analysis parameters saved with each run
- [ ] Tests for file loading, shape checks, frequency-axis generation, and key numerical routines

## E. Data and results

- [ ] Small example dataset that can be committed to Git
- [ ] Larger dataset hosted at a stable external location if necessary
- [ ] SHA-256 checksum and file size for each external dataset
- [ ] Machine-readable metadata file (JSON/YAML/CSV)
- [ ] Expected plot/result and a command that regenerates it
- [ ] Results separated by configuration: single-B210, two-B210 bench, two-element on-sky, four-element on-sky, and RFSoC
- [ ] Limitations and known failure modes stated next to the results

## Release acceptance criteria

A workflow is ready to be called reproducible when a new user can:

1. Install the documented environment on a clean machine.
2. Run the example analysis without access to the original acquisition computer.
3. Regenerate the example figure from the distributed sample data.
4. Identify the exact acquisition code and settings associated with the dataset.
5. Understand which conclusions are bench tests, calibrated observations, or on-sky validation.

Do not commit multi-gigabyte raw recordings by default. Prefer a small, representative sample with clear licensing/permissions, checksums, and instructions for obtaining the full dataset.
