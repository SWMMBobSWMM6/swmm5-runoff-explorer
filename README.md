# SWMM5 Runoff Engine Explorer

Interactive browser app that exposes the EPA SWMM5 runoff engine. Cash-Karp RK5 ODE solver, three runoff surfaces (A1/A2/A3), seven design storms, three infiltration models (Horton / Green-Ampt / CN), `.INP` export, `.RPT` comparison, and `.OUT` binary reader.

## Features

- Animated three-surface cross-section with playback controls
- Live RK5 stage visualization (k₁–k₆ for all three surfaces)
- Adaptive sub-step trace export
- US/SI unit toggle
- Self-validation regression test suite
- ICM InfoWorks ↔ SWMM5 parameter mapping
- Drop a `.RPT` file to compare against EPA SWMM5
- Drop a `.OUT` binary to overlay the real engine's time series

## Usage

Open `index.html` in any modern browser. No install, no server, no dependencies.

## License

The Unlicense (public domain) — same spirit as EPA SWMM5.
