# MicroPython as an ESP-IDF component
-----

This repository represents a proof-of-concept for consuming the
[MicroPython][micropython] project as an [ESP-IDF][esp-idf] component.  This
allows users to remove an extensive amount of boilerplate and copy-pasta
resulting in much cleaner downstream builds.

This work was inspired by [BadgePython][badgepython] and serves as a guide for
the structure of it's `v0.2+` release.

## Prerequisites

- ESP-IDF v4.4+
- git
- CMake 3.12+

## Setup

Clone the directory and initialize the submodule `external/micropython`.

- Via a single command:

```bash
git clone --recurse-submodules https://github.com/brianredbeard/mp-test.git
cd mp-test
```

- In multiple discrete steps

```bash
git clone https://github.com/brianredbeard/mp-test.git
cd mp-test
git submodule update --init --recursive
```

If you just want to build the `GENERIC` example which should run on a base ESP32
board, run:

```bash
idf.py build
```

If you wish to define a new board, start by consulting the [MicroPython manifest
files][mff] documentation.  In that documentation you will find information on
the structure of a "board", high level APIs available, and how to "freeze"
specific Python code into your compiled firmware.

Additional references can be found in the [MicroPython Boards Source][mbs].


[micropython]: https://github.com/micropython/micropython
[esp-idf]: https://docs.espressif.com/projects/esp-idf/en/v4.4.5/esp32/api-guides/build-system.html
[badgepython]: https://github.com/badgeteam/badgePython
[mff]: https://docs.micropython.org/en/latest/reference/manifest.html
[mbs]: https://github.com/micropython/micropython/tree/33b403dfb4ea9ec90427e7228c53e0ac4d333863/ports/esp32/boards

<!--
vim: ts=2 sw=2 et sts tw=80
-->
