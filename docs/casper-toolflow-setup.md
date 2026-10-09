# CASPER Toolflow and `casperfpga` Environment Setup

This guide records the Python/CASPER software environment used during development of the instructional radio interferometer, including work toward a ZCU216 RFSoC backend. It is intended to help another user recreate the environment without relying on commands remembered from the original workstation.

> **Scope:** Installing `casperfpga` provides Python tools for communicating with CASPER hardware. It does **not**, by itself, install the complete FPGA design/build toolchain. Building designs for the ZCU216 also requires a compatible MATLAB/Simulink and Xilinx Vivado installation, the appropriate CASPER `mlib_devel` branch, and platform-specific configuration. Check the official compatibility matrix before choosing versions.

## Environment used in this project

| Item | Project setup / note |
|---|---|
| Operating system | Ubuntu 20.04 LTS |
| Python | Python 3.8.10 |
| Python environment | Isolated virtual environment |
| CASPER Python library | `casperfpga`, using the `py38` branch |
| Hardware development target | Xilinx ZCU216 RFSoC |
| Known installation issue | `bdist_wheel` missing; install/upgrade `wheel` inside the active virtual environment |

This records the setup used during project work, not a guarantee that every combination of dependencies will work on a fresh machine. Record the exact package versions and Git commit hashes from your own successful installation.

## 1. Install system prerequisites

On Ubuntu 20.04, install Git, Python 3.8 development/venv support, and build tools:

```bash
sudo apt update
sudo apt install -y git build-essential python3.8 python3.8-venv python3.8-dev
```

Check the interpreter:

```bash
python3.8 --version
# Expected for the recorded environment: Python 3.8.10
```

If `python3.8` is unavailable, confirm that the operating system and package sources match the intended environment rather than silently substituting a different Python version.

## 2. Create and activate a virtual environment

Choose a workspace directory and keep the environment separate from the repository:

```bash
mkdir -p ~/casper-work
cd ~/casper-work
python3.8 -m venv casper_venv
source casper_venv/bin/activate
```

Confirm that the environment's interpreter and pip are active:

```bash
which python
python --version
python -m pip --version
```

The paths should point inside `~/casper-work/casper_venv/`. Activate this environment in every new terminal before running the installation or Python checks:

```bash
source ~/casper-work/casper_venv/bin/activate
```

## 3. Upgrade packaging tools

A packaging error mentioning `bdist_wheel` can occur when `wheel` is missing from the active Python environment. Install the packaging tools **inside the virtual environment**, not with `sudo pip`:

```bash
python -m pip install --upgrade pip setuptools wheel
```

If the error persists, first verify that `which python` and `python -m pip --version` point to the virtual environment. Avoid changing the system Python or mixing system packages with virtual-environment packages.

## 4. Install `casperfpga`

The project used the `py38` branch for Python 3.8. Clone the official repository and install its requirements:

```bash
cd ~/casper-work
git clone https://github.com/casper-astro/casperfpga.git
cd casperfpga
git checkout py38
python -m pip install -r requirements.txt
python -m pip install .
```

If the repository already exists, inspect its current branch and local changes before switching branches or pulling updates:

```bash
git status
git branch --show-current
git rev-parse HEAD
```

Save the commit hash for reproducibility. Do not blindly update a working environment mid-project; a newer commit can change dependencies or behaviour.

## 5. Verify the Python installation

Move out of the cloned source directory before importing the package. This avoids accidentally importing files from the checkout instead of the installed package.

```bash
cd ~/casper-work
python - <<'PY'
import sys
import casperfpga

print("Python executable:", sys.executable)
print("casperfpga module:", casperfpga.__file__)
print("casperfpga version:", getattr(casperfpga, "__version__", "version attribute unavailable"))
PY
```

**Success criteria:** the import completes without an exception, and the module path points to the active virtual environment. Save the output with your environment notes.

## 6. Toolflow build environment for ZCU216

The Python library and the FPGA build toolflow are separate parts of the setup.

For the FPGA build toolchain:

1. Read the [official CASPER toolflow installation guide](https://casper-toolflow.readthedocs.io/en/latest/src/Installing-the-Toolflow.html) and its compatibility matrix.
2. Install the matching MATLAB/Simulink and Xilinx Vivado versions, including the required licences and supporting components.
3. Clone `mlib_devel` using the branch compatible with the installed tool versions and target platform. The official compatibility matrix lists ZCU216 combinations by Vivado release and corresponding `mlib_devel` branch; do not choose a branch independently of those versions.
4. Activate the same intended Python environment and install the requirements from the selected `mlib_devel` checkout as instructed by the official documentation.
5. Complete the local toolflow configuration using the paths for your own MATLAB and Vivado installations.
6. Build a known tutorial design for the selected platform before attempting the project's correlator design.

Example clone command **only after confirming the correct branch**:

```bash
git clone -b <compatible-branch> https://github.com/casper-astro/mlib_devel.git
cd mlib_devel
source ~/casper-work/casper_venv/bin/activate
python -m pip install -r requirements.txt
```

Replace `<compatible-branch>` with the branch required by the exact Vivado/MATLAB/platform combination. The command above is a template, not a complete build recipe.

Official references:
- [CASPER Toolflow installation and compatibility matrix](https://casper-toolflow.readthedocs.io/en/latest/src/Installing-the-Toolflow.html)
- [Install `casperfpga`](https://casper-toolflow.readthedocs.io/en/latest/src/How-to-install-casperfpga.html)
- [Official `casperfpga` repository](https://github.com/casper-astro/casperfpga)
- [CASPER RFSoC getting-started tutorial](https://github.com/casper-astro/tutorials_devel/blob/main/docs/tutorials/rfsoc/tut_getting_started.rst)

## 7. Hardware communication check

Only run this after the ZCU216 has a compatible programmed image, is powered, and is reachable on the network. Replace the example address with the board's configured IP address:

```python
import casperfpga

fpga = casperfpga.CasperFpga("<ZCU216-IP-address>")
print(fpga.is_connected())
```

A successful connection tests communication with the board; it does **not** prove that a custom correlator design is built correctly or producing valid visibilities.

## 8. Capture a reproducible environment record

From the activated environment, save the following output alongside your project notes:

```bash
mkdir -p ~/casper-work/environment-record
python --version > ~/casper-work/environment-record/python-version.txt
python -m pip freeze > ~/casper-work/environment-record/pip-freeze.txt
git -C ~/casper-work/casperfpga rev-parse HEAD > ~/casper-work/environment-record/casperfpga-commit.txt
git -C ~/casper-work/casperfpga branch --show-current > ~/casper-work/environment-record/casperfpga-branch.txt
```

Also record:
- Ubuntu release (`lsb_release -a`)
- MATLAB and Vivado versions and licence availability
- `mlib_devel` branch and commit hash
- target platform and board image/firmware version
- exact commands used and any fixes needed
- the first successful tutorial build and its log

Do not commit machine-specific IP addresses, credentials, licence files, or private paths.

## Troubleshooting

| Symptom | First checks |
|---|---|
| `bdist_wheel` / wheel build error | Activate the venv, then run `python -m pip install --upgrade pip setuptools wheel`. |
| `ModuleNotFoundError: casperfpga` | Check the active interpreter, installation output, and whether you ran the test outside the source checkout. |
| Import works only inside the cloned repository | Change to another directory and inspect `casperfpga.__file__`. |
| Dependency installation fails | Capture the full error and versions; check whether a dependency requires a particular Python/pip version. Avoid random package downgrades in the main environment. |
| Python library installs, but FPGA build fails | Check MATLAB/Simulink, Vivado, licences, `mlib_devel` branch, and toolflow configuration. `casperfpga` alone is not the build toolchain. |
| Cannot connect to ZCU216 | Verify board power, Ethernet/IP/subnet, reachability, programmed image, and the expected control interface. |

## Reproducibility status

This guide documents the known project environment and installation route. Before describing the complete ZCU216 build as fully reproducible, add the exact working MATLAB/Vivado versions, `mlib_devel` branch and commit, dependency lock/snapshot, local configuration procedure, and a verified tutorial-build log. Those details depend on the actual workstation installation and should not be guessed.
