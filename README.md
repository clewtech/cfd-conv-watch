# CFD Convergence Watch

A lightweight desktop tool for visualizing and analyzing CFD solver convergence history.

![CFD Convergence Watch](docs/screenshot_v030.jpg)

## Features

- Load monitor point history files from multiple CFD solvers
- Interactive chart with pan, zoom, and auto-fit
- Dual Simple Moving Average (SMA) for convergence evaluation
- Convergence overlay with color-coded indicators (OK / ~ / X)
- Statistics table with clipboard copy (Excel-compatible)
- Dark, Light, and Classic themes

## Supported Solvers

| Solver | Type | File Format |
|--------|------|-------------|
| Generic CSV | - | `.csv` |
| Ansys CFX | Steady | `.csv` |
| FlexCompute Flow360 | Steady / Transient | `.csv` |
| Ansys Fluent | Steady / Transient | `.out` |
| SU2 | Steady | `.csv` |

## How to Use

1. Set up user-defined monitor points in your CFD solver and run the simulation.
2. Open the exported monitor history files (CSV or OUT) in CFD Convergence Watch.
3. Select the matching solver preset from **Settings > CFD Solver**.
4. Check the variables you want to inspect and quickly evaluate convergence using the SMA overlay and convergence indicators.

## Download

Download the latest release from the [Releases](../../releases) page.

## Documentation

- [User Manual (English)](docs/user_manual_en.md)
- [User Manual (Korean)](docs/user_manual_ko.md)

## System Requirements

- Windows 10 or later
- DirectX 11 compatible GPU

## Acknowledgments

Built with [Dear ImGui](https://github.com/ocornut/imgui) and [ImPlot](https://github.com/epezent/implot) (MIT License).

## Trademarks

ANSYS, CFX, and Fluent are registered trademarks of ANSYS, Inc. Flow360 is a trademark of FlexCompute, Inc. SU2 was developed at Stanford University. This software is not affiliated with, endorsed by, or sponsored by any of these organizations.

## License

Freeware - free for personal and commercial use. No warranty.

Copyright 2025 CLEW, Inc.
https://www.clew.tech
