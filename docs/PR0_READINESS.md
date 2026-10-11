# PR0 source readiness

**State: EXPERIMENTAL source preparation only.** No repository visibility, release, or publication state changed.

- **Baseline source commit before this preparation:** b09b6e1cb68ac17cf5e68bdaa867fefcd2e0121c.
- **Supported code target:** portable C99 helper library; C headers also support C++ inclusion. This repository is not a standalone game. It is used in Steam Frame ports, including the linked Vice City and GTA San Andreas projects.
- **License:** root LICENSE is the original MIT text copied from the sfvr source in the Steam Frame playbook. It grants rights to this library only, not to any game-port source, game assets, ROM or commercial data.
- **Build route:** from a fresh checkout, run make test; optional sanitizer route is make sanitize. See STANDARD.md for behavior conventions.

The immutable PR0 test source commit, supported targets and final check results are in [PR0_METADATA.json](PR0_METADATA.json).

## Evidence

| Check | Result |
|---|---|
| Fresh isolated clone with a temporary HOME and global/system Git config disabled | Passed. |
| make test | Passed: strict C99 build, standalone C/C++ header checks, C++ linkage, and all unit tests (including adjust and camera modules). |
| Sanitizers | Not run for this source-preparation task. |
| Device/headset acceptance | Not applicable to this helper-only library; the consuming game ports have separate acceptance gates. |

## Limits

No runtime performance, controller, tracking, stereo or game-play claim is made by this library repository. Each consuming port must validate its own integration and user experience. The previous root README is preserved in [README_PRE_PR0.md](README_PRE_PR0.md).

ROM-free check command run from the isolated clone:

~~~sh
make test
~~~
