# Copilot instructions for hello-kivy

## Project overview

This repository is a minimal Kivy app used to produce an Android APK via `python-for-android`. The app lives in `src/main.py`, and the build pipeline is defined in `.github/workflows/build.yaml`.

The README documents how to build the APK locally with the same Docker image used by CI. This is not a larger Python package or service.

## Build, test, and validation commands

There is no dedicated Python test suite or lint configuration in this repository.

Use these commands when validating changes:

- Local smoke test for the app itself (requires Kivy to be installed in the environment):
  - `python src/main.py`
- Quick syntax sanity check for the Python source without requiring the full UI runtime:
  - `python -m compileall src`
- Android APK build used by CI (from `.github/workflows/build.yaml`):
  - `p4a apk --private src --name hello_kivy --dist_name hello_kivy --package de.deac0.hello_kivy --version 0.1 --bootstrap=sdl2 --requirements=python3,kivy --arch=armeabi-v7a`

CI behavior:

- GitHub Actions runs the Android build inside the `ghcr.io/kaplan2539/p4a-container` container; the README has the equivalent local Docker command.
- It uploads the built APK as an artifact and publishes a GitHub Pages download page with a PNG QR code and APK link.
- The workflow is the authoritative validation path for packaging changes.

## High-level architecture

This is a small, single-file Kivy application:

- `src/main.py` contains the entire app.
- `MyPaintApp` subclasses `kivy.app.App` and returns `MyPaintWidget` from `build()`.
- `MyPaintWidget` handles touch input and draws directly on the Kivy canvas.

The app behavior is intentionally simple and stateful in a single widget:

- `on_touch_down`: creates a yellow circle and a `Line` object for the current touch.
- `on_touch_move`: appends points to the active line.
- `on_touch_up`: clears the widget canvas.

The repository does not have a larger layered architecture or service boundary. Packaging is the main concern: source files are bundled into an Android app using `python-for-android`. GitHub Pages only hosts the APK download page.

## Key conventions

- Keep the project structure minimal. Most changes should remain in `src/main.py` unless there is a strong packaging need to add assets or additional modules.
- Prefer Kivy idioms already used here (`App`, `Widget`, `canvas`, touch handlers) over introducing other UI or rendering layers.
- Treat Android packaging compatibility as a first-class concern. Keep Python code standard-library friendly and compatible with the `requirements=python3,kivy` build configuration in CI.
- When changing behavior, validate against the same touch-based drawing flow the app is designed for; this is a direct interactive app, not a CLI or web app.
- There are no existing lint, formatter, or test conventions to follow beyond the APK build workflow and local Python sanity checks.
