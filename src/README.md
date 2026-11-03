# iec104_cli, payload source

`iec104_cli` is a command-line IEC 60870-5-104 master used by the Caldera
[iec104](https://github.com/mitre/caldera-ot) plugin abilities. It wraps
the open-source [`c104`](https://pypi.org/project/c104/) library and exposes one
subcommand per master action.

The payload **comes precompiled** with releases of this repository, but can be
rebuilt from this directory.

## Layout

```
src/
├── iec104_cli.py        # argparse entrypoint
├── iec104_cli.spec      # PyInstaller one-file spec (bundles c104)
├── iec104/              # the action library
│   ├── client.py        # IEC104Client (connect + master actions)
│   └── version.py
├── Makefile             # build targets
└── requirements.txt     # c104
```

## Actions

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

Exit code `0` means the outstation confirmed the request (ACT-CON positive), `1`
means no positive confirmation, `2` means a connection or argument error.

## Build

Local (needs Python 3.10+ and a venv):

```
make build/local                                             # -> dist/iec104_cli
make update DEST=<caldera>/plugins/iec104/payloads           # install it
```

Reproducible cross-platform builds (Docker, matching the CI images):

```
make build/linux      # -> dist/iec104_cli
make build/windows    # -> dist/iec104_cli.exe
```

## Compatibility

The published Linux binary is built by the release CI on the `ubuntu-22.04` runner
(**glibc 2.35**), so it requires glibc >= 2.35. A locally built binary inherits the
glibc of its build host, so build on the oldest distribution you need to support.
Windows binaries are produced by the release CI. The wrapped `c104` library is
copyleft-licensed, see `NOTICE.md` at the repository root.
