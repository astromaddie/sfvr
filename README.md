# sfvr — shared Steam Frame VR helpers

**EXPERIMENTAL support library.** [Canonical source: astromaddie/sfvr](https://github.com/astromaddie/sfvr) is a small C99 library for common VR port behavior: comfort turning, recenter timing, room-scale movement, haptics, settings keys, live camera adjustment and performance log formatting. It has no OpenXR, graphics or SDL dependency and is not a game or standalone application.

The library is used by the [Vice City VR port](https://github.com/astromaddie/vicecity-vr), [GTA San Andreas VR port](https://github.com/astromaddie/gtasa-vr) and other Steam Frame experiments. The complete module list and conventions are in [STANDARD.md](STANDARD.md). Port copies may contain local changes; check each copy's `VERSION` and `README.md` before syncing.

## Status

This is experimental support code. A fresh isolated checkout at `57bb01309effea58df2d91b24836b4b7cd41f1c4` passed `make test`, including nine unit suites and C/C++ header/linkage checks; it has no independent headset acceptance claim. Game ports must still validate their own controller mapping, tracking, stereo rendering, gameplay, comfort and performance.

| Check | Route | Scope |
|---|---|---|
| Host build and unit suite | `make test` | C99 modules, standalone headers, C++ linkage and unit tests |
| Sanitizers | `make sanitize` | ASan/UBSan test run where supported |

## Build and test

```sh
git clone https://github.com/astromaddie/sfvr.git
cd sfvr
make test
```

Use this library only under the included [MIT License](LICENSE). It contains no game code, ROMs or commercial game data. The previous root README is preserved in [docs/README_PRE_PR0.md](docs/README_PRE_PR0.md).

Immutable tested source, final checks and open gates: [PR0 metadata](docs/PR0_METADATA.json).
