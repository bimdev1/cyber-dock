# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2026-02-10

### Added

- **Hardware Design Prep (v1.0):** Complete set of architectural documentation (`01_vision.md` through `08_hardware_validation.md`).
- **Power Architecture:** Defined 5V/3.3V buck cascade (TPS54560, TPS54332) and specific LDOs for peripheral rails (1.1V, 2.5V).
- **Security Features:** Implemented CM5-controlled Force Power Off circuit via TI TPS2595 eFuse.
- **Interfaces:** Finalized 20-pin FFC pinout with ground interleaving and CM5 GPIO mapping.
- **Validation:** Established decoupling strategy, test point map, and structured bring-up procedure.
- **Research:** Comprehensive project feasibility, competitive analysis, and premium component selection.
- **Scaffolding:** Initial repository setup with LICENSE, .gitignore, and README.
