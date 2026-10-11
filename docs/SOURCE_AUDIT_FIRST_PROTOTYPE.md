# Source Audit — First Prototype

This is a source-level reconnaissance note. It is not a build report: no local compiler/toolchain or legally obtained baserom has been used here, and no ROM has been built or tested.

## Confirmed extension points in the current Snowboard Kids 2 tree

| System | Files | Initial finding |
|---|---|---|
| Player/hit reactions | `src/race/hit_reactions.c`, `include/race/hit_reactions.h` | Existing state transitions include projectile hits, collision knockback, pull, hit stun, frozen/shrink/parachute effects. `setPlayerCollisionKnockbackState` caps strength at `0x60000` and stores an angle plus a velocity component. This is a useful place to study the race game's current knockback semantics, not evidence that Smash damage scaling already exists. |
| Player/track collision | `src/race/track_collision.c`, `include/race/track_collision.h` | Exposes player-to-player collision, vulnerable-player proximity searches, point-vs-player collision, oriented-area collision, and track-wall collision. These APIs could support attack hit checks, but the semantics and update order need tracing before use. |
| Race lifecycle | `src/race/race_session.c`, `src/race/race_main.c`, `include/race/race_session.h` | Race session includes race initialization and viewport setup. The session code defines separate camera/root/overlay viewport slots and a reduced far plane for multiplayer. It is a likely integration point for start/finish and camera work. |
| Course pipeline | `config/courses/*.yaml`, `tools/generate_course_definitions.py`, `include/race/course_definition.h` | Course YAML files describe stock course IDs, asset symbols, environment, render lists, and overlay behavior. The course documentation says generated definitions preserve the matching build's stock table layout. A brand-new course is more than renaming YAML: it needs valid geometry/assets and a deliberate build strategy. |
| Build and assets | `README.md`, `Makefile` | This is a matching decompilation, not a clean-room engine or PC port. Its documented flow extracts assets from a user-provided big-endian ROM and verifies an identical output. The baseline matching build must be preserved as a reference while developing a non-matching gameplay fork. |

## Important architectural implication

The repository is organized to reproduce the original game exactly. Its generated course tables, ROM asset symbols, overlay loading, and linker/layout constraints are built around the original ROM. A new game can still use the codebase as a starting point, but new course geometry and changed gameplay will intentionally stop matching the original ROM. We should keep the upstream matching build available as a baseline and introduce a separate project build/configuration path rather than weakening matching checks or overwriting reference assets without a plan.

## Smash source integration approach

The SSB64 decompilation should be analyzed as a second, separate codebase. Do not copy its whole fighter engine into the SK2 engine. First extract a minimal behavioral spec for one attack: startup/active/recovery frames, attack volume, target selection, hit-stun duration, knockback vector, invulnerability/rehit rules, and how the racing player resumes control. Then implement a small race-native combat module against the SK2 player/collision APIs. Direct code reuse should wait until licenses, code provenance, data layouts, and assumptions have been reviewed.

## Next engineering tasks

1. Obtain a reproducible local checkout with submodules and toolchain; record the baseline build result using the user's locally supplied ROM.
2. Trace the actual per-frame player update path and where `hitReactionState`, `knockbackVelocity`, player collision, and track-wall collision are processed.
3. Trace course geometry loading and identify the smallest test route format that can be authored without using a stock course.
4. Add a compile-time-gated test combat module with one attack, keeping original matching behavior isolated.
5. Only after the first new test course loads, tune camera, HUD, and player-count performance.

## Validation status

- Source files and headers inspected through the repository.
- Local build: **not run**.
- Emulator boot: **not tested**.
- New course: **not implemented**.
- Combat integration: **not implemented**.
- ROM artifact: **not produced**.
