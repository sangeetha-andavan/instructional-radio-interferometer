# Observation log and data products

Keep one record per observing session. This page is a schema and index, not a substitute for the original observing logs. Add a separate machine-readable metadata file and a processing command for every released dataset.

## Thesis solar drift scans

The MSc thesis reports four two-element solar drift-scan sessions near 1.420 GHz, with east–west baselines of 1.00 m, 1.80 m, 2.00 m, and 2.40 m. Each session lasted approximately three hours around solar transit. The thesis reports fringe detections in all four sessions and compares measured periods with session-averaged Earth-rotation predictions.

| Baseline | Measured fringe period | Predicted period | Thesis summary |
|---:|---:|---:|---|
| 1.00 m | 44.4 ± 2.8 min | 49.8 min | 3 peaks |
| 1.80 m | 25.6 ± 1.5 min | 26.5 min | 6 peaks |
| 2.00 m | 23.2 ± 1.8 min | 24.9 min | 6 peaks |
| 2.40 m | 17.5 ± 2.0 min | 20.8 min | 9 peaks |

These are thesis-reported summary values. Before reproducing the plots, add the corresponding raw/processed data, exact script version, session timestamps, metadata, and fringe-fitting method. Do not infer missing file names or timestamps.

## Post-thesis observations

Work after the thesis includes updated GNU Radio acquisition/processing, additional solar observations, Galactic Centre observations and calibration work, and four-element on-sky solar validation. These should be added as distinct entries after the original metadata and analysis outputs are available. Do not merge them into the thesis table above.

## Session metadata template

Create one file per session (for example, **metadata/session_id.yaml**) with fields such as:

- session_id:
- project_stage: thesis | post-thesis
- target:
- observing_mode:
- start_time_utc:
- end_time_utc:
- site:
- antenna_count:
- baseline_layout:
- pointing_azimuth_deg:
- pointing_elevation_deg:
- centre_frequency_hz:
- sample_rate_hz:
- fft_length:
- channel_spacing_hz:
- integration_seconds:
- saved_time_step_seconds:
- receiver_gain_settings:
- clock_source:
- time_source:
- pps_distribution:
- calibration_method:
- flowgraph_path_and_version:
- acquisition_script_path_and_version:
- analysis_script_path_and_version:
- raw_data_files:
- file_sizes_bytes:
- sha256_checksums:
- quality_flags_or_overflows:
- processing_command:
- outputs:
- notes_and_limitations:

## Galactic Centre observing

Add each Galactic Centre session as its own record. Include the target coordinates/pointing, baseline vector, observation window, frequency setup, calibration state, and whether the result is preliminary or calibrated. A feature in a spectrum or visibility waterfall should not be described as a detected H I line until its frequency reference, bandpass, RFI environment, and calibration have been checked.

## Four-element on-sky solar validation

This needs a separate record from the four-element injected-tone bench validation. Include the four antenna positions, all six baseline vectors, the reference/PPS distribution, the timed-start implementation, calibration strategy, observation duration, raw visibility products, and per-baseline fringe/phase diagnostics. Add a summary only after those files are available and the analysis can be rerun.

## Recommended data-release layout

- **metadata/** — one YAML or JSON record per observation
- **data/examples/** — small example data that can be committed to Git
- **external archive** — larger raw recordings, with stable links and checksums
- **results/** — reproducible plots and compact result tables
- **python/** — analysis scripts and documented command-line entry points
