# Multi-device synchronisation guide

This guide describes the synchronisation approach tested for the four-channel, two-USRP B210 configuration in the MSc thesis *Design and Development of an Instructional Interferometer for Radio Observations* (IIT Indore, May 2026). It separates frequency reference, time alignment, and residual phase calibration because these are related but distinct requirements.

> **Reproducibility status:** This is the documented procedure and diagnostic guide, not yet a substitute for the exact acquisition script. The version-controlled UHD/Python synchronisation script, device serial numbers, wiring diagram, and tested software versions must be added before this guide is fully reproducible end to end.

## 1. Why synchronisation matters

An FX correlator forms a spectral cross-product such as V_ij[k] = X_i[k] X_j*[k] for each antenna pair and frequency channel. A changing relative receiver phase can obscure the phase evolution due to the astronomical source. A useful multi-device system therefore needs stable relative timing and phase during an observation.

Three concepts should not be conflated:

- **Frequency reference:** gives independent devices a common frequency standard.
- **Sampling-time alignment:** makes devices begin acquisition at a known common time.
- **Residual phase calibration:** measures and corrects the stable instrumental phase offset that can remain after synchronisation.

A shared frequency reference does not, by itself, force independent synthesiser PLLs to start at the same phase.

## 2. What the project established

### RTL-SDR daisy-chain

A shared 28.8 MHz reference was tested between RTL-SDR Blog V3 dongles. The bench cross-correlation did not maintain a stable phase. This is a useful negative control: sharing a reference frequency was not sufficient for this setup.

### Two channels on one USRP B210

The two receive channels share the AD9361 transceiver and local-oscillator architecture. A split continuous-wave (CW) tone was used to test the cross-spectrum and phase stability before on-sky observations.

### Two USRP B210 devices

The thesis configuration used:

- a common external 10 MHz reference distributed to both devices;
- a common 1 pulse-per-second (PPS) signal;
- UHD external clock and time sources;
- explicit PPS-aware timing control in the Python code exported from GNU Radio Companion;
- a common scheduled acquisition start time.

The thesis reports that default GRC-generated initialisation was not sufficient for repeatable inter-device alignment in this setup. The exported Python flowgraph was modified to control the UHD timing sequence explicitly.

## 3. Four-channel mapping and six cross-products

The thesis labels the four receive channels as A1, A2 (USRP 1, channels 0 and 1) and B1, B2 (USRP 2, channels 0 and 1).

| Baseline | Pair | Type |
|---|---|---|
| A1–A2 | Within USRP 1 | Internal baseline |
| A1–B1 | Across USRPs | Cross-device baseline |
| A1–B2 | Across USRPs | Cross-device baseline |
| A2–B1 | Across USRPs | Cross-device baseline |
| A2–B2 | Across USRPs | Cross-device baseline |
| B1–B2 | Within USRP 2 | Internal baseline |

The two internal baselines provide useful references because each pair is within one B210. The four cross-device baselines test synchronisation between the two radios.

**Do not rely on labels alone when wiring hardware.** The repository should include a diagram mapping each antenna/front end, cable, USRP serial number, channel, and correlator baseline. Confirm the actual mapping used for each observing campaign before applying this table to later data.

## 4. Bench test before connecting antennas

1. Connect a known CW signal to a suitable RF splitter or power divider.
2. Feed the split signal to the channels being compared, keeping input levels within the receiver's safe operating range.
3. Verify that both USRPs are detected and connected over USB 3.
4. Configure both devices for the intended external reference and PPS sources.
5. Verify reference lock and inspect UHD/device status before recording.
6. Run the synchronisation-aware acquisition script.
7. Inspect cross-spectrum amplitude and phase for all relevant baselines.
8. Repeat the test after a device restart to measure run-to-run phase-offset changes.
9. Save the raw or minimally processed test output, configuration, software versions, and plots.

A correlated tone should appear at the expected frequency. Stable phase over a run is a necessary coherence check; a fixed phase offset that changes after a power cycle should be recorded and handled by calibration when required.

## 5. Timing sequence documented in the thesis

The thesis describes replacing default GRC initialisation with explicit UHD API control. In outline, the sequence was:

1. Wait for a valid PPS transition and inspect the last PPS time with the UHD API.
2. Schedule both devices' time reset for the next PPS edge using the UHD call set_time_next_pps(0.0).
3. Schedule a common future acquisition start with set_start_time(...).
4. Start streaming and check that both devices begin on the intended common time boundary.

The thesis notes that calling time-setting functions sequentially can make devices act on different PPS edges. The implementation issued the time-setting calls in separate threads; a small command-time offset associated with Python thread scheduling remained possible. This is why the code, timing logs, and empirical phase checks must accompany the written procedure.

**Do not copy these API names into a new script without checking the installed UHD version and the repository's tested implementation.** The executable source and exact arguments are required for a faithful reproduction.

## 6. Common failure modes and diagnostics

| Symptom | Likely area to inspect | Diagnostic |
|---|---|---|
| Phase sweeps continuously between devices | Independent references, time-source configuration, or incorrect timing initialisation | Repeat the split-tone test; verify external clock/time settings and PPS alignment |
| Phase is stable during a run but changes after restart | Repeatable timing with a different instrumental LO phase offset | Record a calibration tone/phase at the start of each session |
| UHD timeout while setting device time | PPS edge not yet observed or timing commands issued at the wrong point in the PPS cycle | Wait for a valid PPS transition and capture device/time-source logs |
| Buffer-overflow warnings or discontinuous phase | USB throughput, receive-buffer depth, or host processing load | Use USB 3 for both radios; monitor overflow warnings; test FFT size and receive-buffer settings |
| Internal baselines look stable but cross-device baselines do not | Inter-device synchronisation not established | Compare all six baselines with the same injected signal |
| Phase appears noisy where correlation amplitude is near zero | Low SNR makes phase estimates poorly constrained | Examine amplitude/SNR alongside phase; do not interpret phase alone |

The thesis reports that moving both B210s to USB 3 connections, using a 4096-point FFT for the four-channel workload, and increasing receive-buffer depth with num_recv_frames=256 resolved the overflow issue in its tested setup. Treat these as a tested starting point, not universal settings for every host or software version.

## 7. Calibration and on-sky use

A coherent bench test does not remove the need to calibrate the astronomical measurement. The thesis reports a stable, session-dependent LO phase offset after power cycling and identifies calibration as the way to measure and correct it.

For each observing run, record at minimum:

- UTC start/end time and target;
- antenna positions and baseline vectors, with coordinate convention;
- RF centre frequency, sample rate, FFT length, channel spacing, integration time;
- USRP serial numbers, channel mapping, gain settings, clock/time source, and PPS wiring;
- software and flowgraph versions;
- calibration signal/source, calibration timestamp, and applied correction;
- data file names, format, size, and checksum;
- any UHD errors, overflows, restarts, or interruptions.

Later calibration observations and four-element on-sky solar validation should have their own run records and analysis products; do not treat bench coherence as proof of on-sky validation.

## 8. Files needed to make this fully reproducible

The following should be checked into this repository once the exact tested versions are identified:

- exported GNU Radio Python script with the synchronisation modifications;
- corresponding .grc flowgraph;
- a wiring/reference-distribution diagram;
- device serial numbers and channel-to-baseline mapping for the tested setup;
- environment or dependency/version file;
- a safe example configuration with no machine-specific paths;
- a short split-tone dataset and a script that reproduces the phase/amplitude validation plot;
- calibration procedure and a small example showing the phase correction.

See the repository README for the broader system and analysis workflow.
