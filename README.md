# sfvr — shared Steam Frame VR helpers

A small C99 library I use across my Steam Frame VR ports. It keeps the common controls and settings consistent, so switching games doesn't mean relearning how to recentre, turn or adjust the camera.

**EXPERIMENTAL.** This is a support library, not a game or an OpenXR runtime. It has no game-data requirement and no OpenXR, graphics or SDL dependency. The game ports handle their own rendering, controller bindings and file storage.

## What's in it

- Short-press and hold timing for View, including recentring and height calibration.
- Live camera adjustment with grip + stick, with a neutral-stick check before returning to gameplay.
- Camera distance and height offsets, world scale, snap turning and smooth turning.
- Room-scale movement and leaning helpers.
- Haptic events, strength settings and a shared pulse queue.
- Common settings names, ranges and environment overrides.
- A consistent performance log with frame rate and CPU/GPU timings.

The [standard](STANDARD.md) explains the controls and conventions. Each header documents its own API. [Vice City](https://github.com/astromaddie/vicecity-vr), [San Andreas](https://github.com/astromaddie/gtasa-vr) and several other ports use it.

## Build and test

You'll need a C99 compiler, a C++17 compiler for the header checks, and Make. The tests run on the development machine and need no headset or game files.

```sh
git clone https://github.com/astromaddie/sfvr.git
cd sfvr
make test
```

`make test` runs the nine unit suites, compiles the headers individually as C and C++, and checks C++ linkage. `make sanitize` runs the tests with AddressSanitizer and UndefinedBehaviorSanitizer where supported. The [PR0 test record](docs/PR0_METADATA.json) identifies the checked source revision.

## Use it in a port

Copy `include/` and `src/` into your port, add `include/` to the compiler's include path, and build the C files with `-lm`. Include `sfvr/sfvr.h` for everything, or just the header you need.

Keep a `VERSION` file with the sfvr commit you copied, and keep the [MIT license](LICENSE) with it. Port copies can have local changes, so check their version and diff before replacing them. Prefer fixing shared behavior here, then updating the copies.

## License

sfvr is my shared port-support code, licensed under [MIT](LICENSE). That license covers this library; it doesn't give permission to redistribute any commercial game or its data. The [changelog](CHANGELOG.md) lists library changes.
