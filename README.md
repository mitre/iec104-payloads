# Caldera for OT plugin: IEC 104 - Payloads

> Contributed by Palo Alto Networks. Not maintained by MITRE: the payload and its pinned dependency are frozen as contributed and will not be updated.

This repository provides the compiled payload binaries as well as the payload source
code for the [Caldera for OT](https://github.com/mitre/caldera-ot) IEC 104 plugin.

## Quick Start

To use the IEC 104 plugin:
1. Download the appropriate compiled payload from the **Releases** section of this
   repository.
2. Save the downloaded payload file(s) in the `caldera/plugins/iec104/payloads`
   directory of your Caldera installation.

## About

Where possible, the Caldera for OT plugins leverage open-source libraries and
payloads, unifying their exposure through the Caldera Adversary Emulation framework.

- The IEC 104 payload is written in Python using the open-source
  [`c104`](https://github.com/Fraunhofer-FIT-DIEN/iec104-python) library - version
  2.2.

- The `c104` library is licensed with the
  [GPL 3.0 License](https://github.com/Fraunhofer-FIT-DIEN/iec104-python/blob/main/LICENSE).

- A custom command-line interface was developed for the `c104` library to allow for
  the unified exposure of IEC 104 master actions as Caldera ability commands. Because
  the compiled payload bundles `c104`, this repository is licensed **GPL-3.0**, and is
  kept separate from the Apache-2.0 licensed plugin.

## Build From Source

### Source Code Structure

- `src/iec104_cli.py` - the command-line interface used by Caldera agents, which
  dispatches to the action library.
- `src/iec104/` - the action library: a wrapper on `c104` that produces an API suited
  to the needs of a Caldera payload.
- `src/iec104_cli.spec` - PyInstaller spec that bundles `c104` into a single
  self-contained executable.
- `src/Makefile` - local and containerized build targets.
- `tests/` - unit tests that drive the action library against a real `c104`
  outstation on localhost.

### Build System Configuration

| Item | Linux binary | Windows binary |
|:-----|:-------------|:---------------|
| OS ver. | Ubuntu 22.04 (`ubuntu-22.04` runner) | Windows Server 2022 (`windows-2022` runner) |
| Python ver. | 3.10 | 3.10 |
| builder | PyInstaller (one-file) | PyInstaller (one-file) |
| binary name | `iec104_cli` | `iec104_cli.exe` |

macOS is intentionally not built: `c104` has no maintained macOS build (no arm64
wheel, and it does not compile on the macOS runners).

### Step-by-Step Instructions

1. Clone the repository:
```
git clone https://github.com/mitre/iec104-payloads.git
```
2. Build the payload for the host platform:
```bash
# from the src/ directory
make build/local
```
This installs the requirements, runs PyInstaller and places the binary in
`src/dist/`.

3. Copy the newly built binary into the plugin's `payloads/` directory so that it is
   detected and used by Caldera:
```bash
# from the src/ directory
make update DEST=<caldera>/plugins/iec104/payloads
```

### Containerized Builds

`make build/linux` and `make build/windows` reproduce the released binaries in pinned
PyInstaller Docker images, which is the recommended way to build for a platform other
than the host.

### Payload Compatibility

The published Linux binary is built on the `ubuntu-22.04` runner (glibc 2.35), so it
requires glibc 2.35 or greater. A locally built binary inherits the glibc of its build
host, so to support older Linux hosts, build on the oldest distribution you need.

## Testing

The `tests/` directory contains unit tests that stand up a real `c104` outstation on
localhost and drive it with the action library, asserting that the outstation observed
each master action:

```bash
pip install -r src/requirements.txt pytest
PYTHONPATH=src python -m pytest tests -q
```

The same tests run in CI on Linux and Windows before each payload is built, see
[`.github/workflows/ci.yaml`](.github/workflows/ci.yaml). Pushing a `v*` tag builds
both binaries and publishes them as release assets.

## Usage

The `iec104_cli` executable supports the following general usage:

```
iec104_cli <host> [--port 2404] [--ca 1] <action> [args]

  interrogate [--qualifier station|group1..group16]   C_IC_NA_1
  counter-interrogate                                 C_CI_NA_1
  scan                                                C_IC_NA_1 + point listing
  single-command <ioa> <ON|OFF> [--select]            C_SC_NA_1
  double-command <ioa> <ON|OFF> [--select]            C_DC_NA_1
  setpoint <ioa> <value> [--type na|nb|nc] [--select] C_SE_NA/NB/NC_1
  clock-sync                                          C_CS_NA_1
```

Exit code `0` means the outstation confirmed the request (positive ACT-CON), `1` means
no positive confirmation, and `2` means a connection or argument error.

Use `iec104_cli --help` to get started.

## Authors

This payload was contributed by **Asher Davila** ([AsherDLL](https://github.com/AsherDLL)) and **Malav Vyas** ([malavvyas](https://github.com/malavvyas)) of Palo Alto Networks.

## License

GPL-3.0-or-later. See [`LICENSE`](LICENSE) and [`NOTICE.md`](NOTICE.md).
