# System architecture

The interferometer is organised as an analogue RF front end followed by digitisation, channelisation, cross-correlation, and offline analysis. The design operates near 1.420 GHz (the 21-cm neutral-hydrogen line).

## Signal path

For each antenna element:

1. **Antenna:** DIY pyramidal horn designed for operation near 1.42 GHz.
2. **RF front end:** low-noise amplification and filtering. The thesis-selected chain is LNA → band-pass filter → LNA → high-pass filter.
3. **SDR backend:** USRP B210 for the thesis two-element observations. Its two receive channels share the AD9361 local-oscillator architecture.
4. **Digital signal processing:** GNU Radio FX correlator. Each channel is transformed into frequency channels; spectra are multiplied by the complex conjugate of the paired spectrum.
5. **Data products:** complex cross-spectra/visibilities and supporting metadata.
6. **Offline analysis:** quality checks, time/frequency visualisation, calibration where applicable, fringe measurement, and comparison with predictions.

## Two-element configuration

A single USRP B210 digitises the two antenna signals. This avoids inter-device clock alignment for the two-channel case. The thesis used an FX correlator in GNU Radio and recorded complex visibility information for offline analysis.

## Four-element configuration

Two USRP B210 devices provide four receive channels. With four signals, the correlator forms six unique cross-products:

- A1–A2 and B1–B2 are within-device baselines.
- A1–B1, A1–B2, A2–B1, and A2–B2 are cross-device baselines.

The thesis bench configuration used a shared 10 MHz reference, common PPS, and a PPS-aligned timed start controlled through UHD. The fixed instrumental phase offset can change after a power cycle, so phase calibration and on-sky validation are separate steps.

## RF front end documented in the thesis

The thesis describes the Nooelec SAWbird+ H1 LNA, Mini-Circuits VBFZ-1400-S+ band-pass filter, a second LNA, and Mini-Circuits SHP-1000+ high-pass filter. The selected ordering is LNA → BPF → LNA → HPF. The thesis reports approximately 68 dB measured end-to-end gain for the assembled chain; component-level gain estimates and measured end-to-end performance should not be conflated.

## Backends and project evolution

- **RTL-SDR:** early coherence experiments; clock daisy-chaining did not provide the stable inter-device phase needed for the tested interferometer.
- **USRP B210:** thesis two-element solar observations and four-element/six-baseline bench tests.
- **RFSoC ZCU216:** later backend development and familiarisation; keep its design, bitstreams, build instructions, and validation evidence separate from the B210 results.

## Documents required for full reproduction

This overview must be accompanied by original photographs/diagrams, component part numbers, cable and reference-distribution maps, the actual GRC flowgraphs, exported Python acquisition scripts, software versions, configuration files, and example input/output data. See the [synchronisation guide](synchronization.md) and [reproducibility checklist](reproducibility.md).
