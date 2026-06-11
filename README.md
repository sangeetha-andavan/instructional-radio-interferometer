# Instructional Radio Interferometer

A scalable instructional radio interferometer developed at IIT Indore to demonstrate the principles of radio interferometry through real observations and hands-on instrumentation development.

The project combines custom horn antennas, RF front-end electronics, software-defined radio backends, and GNU Radio based digital signal processing to perform interferometric observations at 1.420 GHz.

## Project Goals

* Develop a low-cost instructional radio interferometer
* Demonstrate visibility measurements and interferometric fringes
* Study phase coherence and synchronization in SDR-based systems
* Perform real radio observations using multiple antennas
* Provide an educational platform for learning radio interferometry

## Key Components

### Antenna System

* Custom pyramidal horn antennas
* Designed for operation near the 1420 MHz hydrogen line
* Simulated and characterized using CST Studio Suite

### RF Front-End

* Low-noise amplification
* Bandpass filtering
* Receiver characterization
* Gain and noise figure measurements

### Digital Backend

* RTL-SDR and USRP B210 platforms
* GNU Radio signal processing pipelines
* FX correlator implementation
* Phase synchronization studies

### Observations

* Solar interferometric observations
* Fringe detection and analysis
* Visibility measurements
* Earth-rotation fringe studies

## Current Status

The system has successfully demonstrated:

* Two-element interferometric observations
* Multi-device synchronization
* Multi-baseline correlation
* Solar fringe detection

## Future Development

* Expanded multi-element observations
* RFSoC-based backend development
* Aperture synthesis demonstrations
* Additional astronomical targets
