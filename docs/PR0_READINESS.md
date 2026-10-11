# PR0 source readiness

**EXPERIMENTAL — ready for public source publication.** sfvr is my standalone C99 support library for the Frame ports. It has no game code or game-data requirement.

The fresh build was checked at `c3f69ac72d072cebae0c7a4612d5cb8218bcda06`. `make test` passed all nine unit/header/linkage suites, and `make sanitize` passed the AddressSanitizer and UndefinedBehaviorSanitizer checks. The checkout used its own empty home and no personal Git configuration or credentials.

The Git-history audit found no ROMs, commercial game data, credentials or signing material. The subsequent changes only affect documentation and the original MIT notice; the credential scan of those commits was also clean. There are no source-publication blockers.

The [MIT license](../LICENSE) covers this library. It does not give permission to redistribute commercial games or their data. Each game port handles its own input, tracking, rendering and performance; there is no separate headset application to test here.

See [PR0_METADATA.json](PR0_METADATA.json) for the tested source identity and [CHANGELOG.md](../CHANGELOG.md) for changes. Anonymous GitHub cloning is checked after publication.
