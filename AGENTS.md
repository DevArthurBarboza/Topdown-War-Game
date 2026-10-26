# Repository Guidelines

## Project Structure & Module Organization

This repository contains docs/, rules/, and a user-provided exploratory reference image in docs/references/. It has no Godot project.godot, gameplay runtime, test suite, or configured commands. The selected engine is Godot 4.7 with typed GDScript; MVP exports target Windows and Linux.

The future layout in docs/ARCHITECTURE.md is src/app, feature-grouped src/features, src/ui, src/platform, content/, assets/, and tests/unit plus tests/integration and fixtures. Create these when implementation begins. Read applicable rules before planning code changes.

Maintain docs/ for project changes, including purpose, behavior, and validation. Update rules/ when contributor conventions or architecture decisions change.

## Agent Orchestration

Use an advanced, high-capability AI model to plan each request, define scope, and establish acceptance criteria. Delegate task execution and code writing to a more flexible, fast, lightweight AI model, providing the plan, applicable rules, and expected tests. Make model roles explicit; if the required models are unavailable, report the limitation instead of silently substituting them.

## Build, Test, and Development Commands

No build, development, test, lint, or formatting commands are configured. Do not assume commands work until a project/toolchain exists. When introducing it, document exact Windows/Linux setup, run, export, and test commands in README.md. Select and document a compatible test framework when introducing the toolchain.

## Coding Style & Naming Conventions

Use typed GDScript: snake_case for files/functions/variables, PascalCase for classes and class_name, tabs for indentation, and past-tense signal names for completed facts. Type interfaces and important mutable state. Follow the feature-oriented layout in docs/ARCHITECTURE.md.

## Testing Guidelines

When implementation begins, add meaningful, versioned unit tests alongside gameplay rules for behavior, edge cases, and regressions; avoid trivial getter/setter tests. Run tests and record results in docs/. Run the same headless logic suite on Windows and Linux, then smoke-test both exports and coop with two gamepads and keyboard/mouse plus gamepad. Framework, runner, and CI remain unselected. Visual changes need reproducible manual checks. Target at least 30 FPS on medium hardware; reference hardware, resolution, and stress scene remain undefined.

## Commit & Pull Request Guidelines

Git history is unavailable; use short, imperative commit subjects. Pull requests should explain purpose and validation, link issues, and include media for visible gameplay changes.

## Assets & Configuration

Record source and license for third-party assets. The reference image is not a final asset and its external origin/license is unconfirmed. Never commit credentials or private configuration; provide placeholders instead.
