# Super Smash Boarders — Integration Plan

## Goal

Build a new, native Nintendo 64 game ROM combining snowboard racing with Super Smash Bros.-style combat. Snowboard Kids 2 is the initial technical base because this repository contains its matching decompilation; its original courses and race rules are not the intended final game. This document is a plan, not a claim that the source merge or ROM is complete.

## Core gameplay target

- Race first, fight while racing: steer, accelerate, brake, jump, and manage airtime through purpose-built courses.
- Smash-style combat: attacks create damage/knockback pressure; hits disrupt racing lines and can launch rivals into hazards or off-route.
- Recovery matters: limited aerial control or a character recovery action makes knockback dangerous without making every hit an automatic elimination.
- Standard victory is first across the finish line. Optional rules may add KO bonuses or a combat-focused variant.
- N64-first scope: budget CPU, RSP/GFX memory, textures, audio, and split-screen visibility from the start. Four-player support is a target to validate, not a guarantee before profiling.

## First playable milestone

1. A bootable ROM prototype with one test course that is not an original Snowboard Kids 2 course.
2. One controllable test character with snowboard/race movement.
3. One attack with hit detection and visible knockback.
4. A finish condition and reset/retry path.

Do not start by porting the full SSB roster. Prove the movement/combat loop and performance budget first.

## Course concepts (design placeholders)

- **Hyrule Castle Mountain Run:** castle approaches, rooftop turns, ramps, bridges, and risky shortcuts.
- **Sector Z Starship Circuit:** a Great Fox-inspired route with moving hazards and ranged attack opportunities.
- **Dream Land Whispy Woods Run:** gusts, rolling terrain, and bounded jumps.

These are design targets, not implemented stages. Track geometry and collision should be designed around racing lines and combat encounters rather than merely reskinning existing courses.

## Technical investigation before engine changes

### 1. Establish the baseline
- Record the exact upstream Snowboard Kids 2 commit and supported build procedure.
- Identify which assets/generated files require a user-supplied, legally obtained ROM; never commit ROM images or extracted proprietary assets.
- Build the unmodified project locally with the required ROM/toolchain and record the result.

### 2. Map Snowboard Kids 2 systems
Trace player input/state, acceleration and steering, slope/ground contact, jumping and collision, course geometry/loading, checkpoints/laps/finish logic, camera, items/weapons, hit reactions, knockback, player reset, split-screen rendering, audio allocation, and memory limits. Identify the smallest safe extension points.

### 3. Audit SSB64 separately
Use the SSB decompilation as a reference for fighter state transitions, attack hitboxes, damage/knockback, action timing, and recovery—not as a drop-in binary/source merge. Check licenses and contribution rules before copying code. Prefer a clean, documented reimplementation of the minimum combat behavior if provenance or incompatible assumptions make direct reuse unsuitable.

### 4. Integrate incrementally
- Add a project-owned gameplay module with explicit interfaces to racing movement and combat.
- Start with one test character and one basic attack.
- Add damage/knockback tuning and recovery.
- Replace the first course with original geometry and test route boundaries, checkpoints, and finish logic.
- Test one player, then two, then four-player performance if rendering and memory budgets allow.
- Keep changes small and document each one.

## Proposed implementation order

1. Baseline audit: build instructions, source map, build status, ROM configuration, constraints.
2. Movement prototype: one character on a new test course with stable steering, jumping, and collision.
3. Combat prototype: one attack, hit detection, knockback, recovery, and race-state interaction.
4. Course pipeline: purpose-built track geometry/data, not the original course layout.
5. Multiplayer/performance: increase player count incrementally and profile demanding scenes.
6. Content expansion: characters, attacks, tracks, items, menus, sound, and polish.

## Prototype acceptance criteria

- Builds from source with a locally supplied ROM and documented toolchain.
- Boots in an emulator; real-hardware testing is a separate validation step.
- Racing movement works on a new course.
- An attack hits another player and produces reproducible knockback/recovery.
- Finish/retry flow works without requiring an original course.
- No ROM images or extracted copyrighted assets are committed.

## Current status

This document records the intended design and engineering sequence only. No cross-project code has been merged and no modified ROM has yet been built or tested.
