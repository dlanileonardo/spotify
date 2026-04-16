---
name: Sync Raspotify env contract
description: Use when adding, removing, or changing environment variables used by the Raspotify container, ALSA config, or librespot startup flags.
---

# Sync Raspotify env contract

## When to use

Use this skill whenever a change touches any runtime variable such as `SPOTIFY_NAME`, `BACKEND_NAME`, `DEVICE_NAME`, `ALSA_SLAVE_PCM`, `ALSA_SOUND_LEVEL`, `VERBOSE`, `EQUALIZATION`, or `INITIAL_VOLUME`.

This repository spreads that contract across multiple files, so small env changes easily become inconsistent.

## Why this is a skill here

This repo has a repeated multi-file maintenance pattern:

- `startup.sh` consumes runtime variables and builds the `librespot` command.
- `asound.conf` depends on env substitution.
- `Dockerfile` defines container defaults.
- `compose.yaml` forwards variables from the host into the container.
- `.env.example` is the user-facing template.
- `README.md` documents supported variables and expected behavior.

If one of these is missed, the container may build successfully but behave differently from the documentation or from the user's `.env`.

## Required checklist

When changing env vars, always verify all of the following:

1. **Runtime usage**
   - Confirm how the variable is used in `startup.sh`.
   - If ALSA templating is involved, confirm it is referenced in `asound.conf`.

2. **Container defaults**
   - Add or update the default in `Dockerfile` with `ENV`.

3. **Compose propagation**
   - Add or update the variable in `compose.yaml`.
   - Prefer `${VAR:-default}` when the host `.env` should control it.
   - Avoid hardcoded compose values when the variable is meant to be configurable.

4. **User template**
   - Add or update the variable in `.env.example`.
   - Keep example values safe and generic.

5. **Documentation**
   - Update `README.md` variable docs and any relevant usage/troubleshooting sections.

## Validation rules

Before finishing, confirm:

- Every variable referenced in `startup.sh` is either documented as optional or exposed through `compose.yaml`.
- Every user-configurable variable appears in `.env.example`.
- Defaults in `Dockerfile`, `compose.yaml`, and `README.md` do not conflict.
- New `librespot` flags are documented with valid values or ranges.
- Shell quoting remains safe for values with spaces.

## Recommended repo-specific workflow

1. Search the variable name across the repo.
2. Update `startup.sh` first so runtime behavior is explicit.
3. Sync `Dockerfile`, `compose.yaml`, and `.env.example`.
4. Update `README.md` last to match the final behavior.
5. If needed, validate with `docker compose config` and a container startup smoke test.

## Current example of why this matters

At the moment, this repo already shows why this skill is useful: variables used by startup logic can drift from what is exposed in `compose.yaml` and `.env.example`. Any future env-related change should use this checklist to keep the contract consistent.
