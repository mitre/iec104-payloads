# NOTICE

`iec104_cli`, the payload for the IEC 60870-5-104 plugin for Caldera for OT.

Copyright (c) 2026 Asher Davila ([AsherDLL](https://github.com/AsherDLL)) and Malav Vyas ([malavvyas](https://github.com/malavvyas)), Palo Alto Networks.

Contributed to MITRE Caldera for OT; this repository is not maintained by MITRE.

This program is free software: you can redistribute it and/or modify it under the
terms of the **GNU General Public License v3.0 or later** (see `LICENSE`).

## Third-party components

The `iec104_cli` payload imports, and its compiled binaries bundle:

- **c104**, a Python IEC 60870-5-104 implementation, licensed under the **GNU General
  Public License v3.0**.
  https://github.com/Fraunhofer-FIT-DIEN/iec104-python

Because the compiled payload bundles the GPL-3.0 `c104` library, this repository is
licensed GPL-3.0. It is kept separate from the Apache-2.0 licensed `iec104` Caldera
plugin, which contains no `c104` code and only invokes this payload as a separate
process. This mirrors the arrangement MITRE uses for the `iec61850` plugin and its
`iec61850-payloads` repository.

- **PyInstaller** is used only as a build tool; its bootloader exception permits
  distributing the resulting binaries under this project's license.
