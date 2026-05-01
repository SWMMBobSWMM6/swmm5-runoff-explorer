# SWMM5 Runoff Engine Explorer

[![License: Unlicense](https://img.shields.io/badge/license-Unlicense-blue.svg)](http://unlicense.org/)
[![Live Demo](https://img.shields.io/badge/demo-live-brightgreen.svg)](https://swmmbobswmm6.github.io/swmm5-runoff-explorer/)
[![SWMM5](https://img.shields.io/badge/EPA_SWMM5-5.2.x-orange.svg)](https://www.epa.gov/water-research/storm-water-management-model-swmm)
[![Single File](https://img.shields.io/badge/deployment-single_file-purple.svg)](https://swmmbobswmm6.github.io/swmm5-runoff-explorer/)
[![No Dependencies](https://img.shields.io/badge/dependencies-zero-success.svg)]()

> Interactive browser-based exploration of the EPA SWMM5 runoff engine — Cash-Karp RK5 solver, three runoff surfaces, seven design storms, three infiltration models, with full bidirectional integration to InfoWorks ICM.

**🌐 Live App:** https://swmmbobswmm6.github.io/swmm5-runoff-explorer/

![SWMM5 Runoff Explorer Screenshot](docs/screenshot-main.png)

---

## What This Is

After fifty years of building, maintaining, and teaching SWMM, I've watched almost everyone treat the runoff engine as a black box. You set Manning's n, pick Horton or Green-Ampt, choose a design storm, click run. Numbers appear. They look reasonable. You move on.

But there's a beautiful piece of mathematics buried inside that black box — a Cash-Karp 5th-order Runge-Kutta solver with adaptive step-size control, simultaneously integrating three coupled nonlinear reservoir equations representing three different surface types (impervious without depression storage, impervious with depression storage, pervious). Every wet-weather time step. For every subcatchment. Across thousands of simulations daily in cities around the world.

This app pulls back the curtain. It's a single HTML file — ~150KB, zero dependencies, no server — that runs the same arithmetic SWMM5 runs in compiled C, but visibly, interactively, in your browser.

## Features

### Core Hydrology
- **Three-surface decomposition** (A1/A2/A3) with independent ODEs solved in parallel
- **Cash-Karp 5th-order Runge-Kutta** ODE solver with adaptive step-size control
- **Seven design storm types**: SCS Type I, IA, II, III; Chicago (Keifer-Chu); Uniform; Triangular
- **Three infiltration models**: Horton, Green-Ampt, SCS Curve Number
- **Internal subarea routing**: OUTLET, IMPERV → Pervious, PERVIOUS → Impervious
- **Constant evaporation** support
- **Engman table lookups** for Manning's n on overland flow

### Visualization
- **Animated cross-section** showing rainfall, water depth, infiltration, and runoff in real time
- **Playback controls**: ▶ play/pause, ⏮ step back, ⏭ step forward, time scrubber, six speed settings (0.25× to 8×)
- **Live RK5 stage display** showing k₁–k₆ values for all three surfaces simultaneously
- **Five synchronized charts**: hyetograph + total hydrograph, three-surface breakdown, surface depths, infiltration & sub-step counts, 4th-vs-5th order solutions
- **US/SI unit toggle** with on-the-fly conversion across all displays

### Engineering Workflow
- **📄 Export .INP**: generates a complete, valid EPA SWMM5 input file from current parameters
- **📊 Export CSV**: full time series with water balance summary
- **📂 Drop .RPT file**: parses Subcatchment Runoff Summary and Flow Routing Continuity, overlays as comparison
- **💾 Drop .OUT binary**: full time-series overlay from the real SWMM5 engine
- **🔬 RK5 Trace export**: every adaptive sub-step (time, h, y₄, y₅, error, accept/reject) for numerical analysis
- **✅ Self-validation suite**: five regression tests with known analytical results
- **Wet time step selector**: 1 / 2 / 5 / 10 / 15 / 30 / 60 minute options to match SWMM5's `WET_STEP`

### Reference Material
- **EPA SWMM5 source code map**: 12 expandable cards covering the runoff-relevant C modules
- **Call flow diagram**: SVG showing `swmm_step()` → `runoff_execute()` → `subcatch_getRunoff()` → three parallel `odesolve()` calls
- **Cash-Karp Butcher tableau** with full coefficients
- **Single-step interactive RK5 explorer** for stage-by-stage visualization

### InfoWorks ICM Bridge
- **Six-step setup guide** for SWMM-compatible runoff configuration in ICM
- **Six parameter mapping tables** (subcatchment-level, A1, A2, A3, Horton, Green-Ampt)
- **Six "gotcha" notes** for common conversion mistakes
- **Ready-to-run Ruby script** using `WSApplication` API to auto-configure runoff surfaces
- **Side-by-side architecture comparison** between flat SWMM5 INP and ICM hierarchical data model

---

## Quick Start

### Just Run It
Open https://swmmbobswmm6.github.io/swmm5-runoff-explorer/ in any modern browser. That's it.

### Run Locally
```bash
git clone https://github.com/SWMMBobSWMM6/swmm5-runoff-explorer.git
cd swmm5-runoff-explorer
# Just open index.html — no build step, no server needed
open index.html         # macOS
start index.html        # Windows
xdg-open index.html     # Linux
```

### Self-Host
Drop `index.html` onto:
- **Netlify**: [app.netlify.com/drop](https://app.netlify.com/drop)
- **GitHub Pages**: enable in Settings → Pages → main branch
- **Replit**: import as a static site
- **Any static host**: Cloudflare Pages, Vercel, S3, etc.

It's a single file — works anywhere that serves HTML.

---

## Verification Workflow

The app is **falsifiable** against the real EPA SWMM5 engine. Here's the verification loop:

```
1. Configure your scenario in the app          ← parameters, storm, infiltration model
2. Click "📄 Export .INP"                       ← downloads SWMM5_Explorer_Model.inp
3. Run that .INP in EPA SWMM5                  ← swmm5.exe Model.inp Model.rpt Model.out
4. Drag the resulting .RPT onto the app        ← side-by-side comparison cards appear
5. Drag the resulting .OUT onto the app        ← real time-series overlays the in-browser RK5
6. Compare peak Q, runoff coefficient,         ← differences color-coded green/gold/red
   continuity error                            ← green = under 2%, gold = 2-10%, red = over 10%
```

When wet time steps match between the app and SWMM5, peak Q typically agrees to within 1-2%.

---

## How It Works

### The Three Surfaces

Each subcatchment splits into three independent runoff surfaces solved in parallel:

| Surface | Fraction | Depression Storage | Infiltration | Description |
|---------|----------|--------------------|--------------| ------------|
| **A1** | %Imperv × %Zero-Imperv | None | None | Impervious surfaces with no DS — fast responder |
| **A2** | %Imperv × (1 − %Zero-Imperv) | Yes | None | Impervious with DS — delayed impervious response |
| **A3** | 1 − %Imperv | Yes | Yes | Pervious — slow responder, may produce zero runoff |

Each surface follows a nonlinear reservoir ODE:
```
dd/dt = (rain − evap − infil) − [W·(1.49/n)·(d − d_s)^(5/3)·√S] / A
```

The three solutions sum to total subcatchment outflow: Q = q₁ + q₂ + q₃.

### Cash-Karp RK5 Adaptive Solver

The solver evaluates the right-hand side at six carefully placed points and combines them in two different ways using the same six evaluations: a 5th-order accurate solution `y₅` and a 4th-order accurate solution `y₄`. The difference |y₅ − y₄| approximates the truncation error, which the algorithm uses to dynamically adapt step size — tiny steps where the ODE is stiff, large steps where it's smooth.

This is why the "Sub-Steps" chart matters: it's a fingerprint of where the storm is numerically hardest. A1's sub-step count spikes during the rising and falling limbs because Manning's (5/3) exponent applied to small depths produces sharply varying derivatives. A3 stays at 1-2 sub-steps throughout because infiltration and depression storage smooth its response.

### Variables Tracked Per Time Step
- Rainfall intensity (in/hr or mm/hr)
- Surface depths d₁, d₂, d₃ (in or mm)
- Outflow rates q₁, q₂, q₃, qt (cfs or m³/s)
- Infiltration rate (in/hr or mm/hr)
- RK5 sub-step counts per surface
- All six k-stage values per surface (live during animation)
- 4th-order vs 5th-order pervious surface depth (for solver visualization)

---

## Architecture

```
swmm5-runoff-explorer/
├── index.html              ← The entire application (single file, ~150KB)
├── README.md               ← This file
├── LICENSE                 ← The Unlicense (public domain)
├── .gitignore              ← Node-style ignore patterns
└── docs/
    ├── screenshot-main.png ← Main app view
    ├── screenshot-rk5.png  ← Cash-Karp deep-dive tab
    └── screenshot-icm.png  ← ICM InfoWorks mapping tab
```

That's it. No `package.json`, no `node_modules`, no build configuration. The HTML file contains all CSS and JavaScript inline for maximum portability.

---

## Why Single-File?

I deliberately rejected the modular ES6 architecture during development. Reasons:

1. **Distribution**: SWMM5 modelers email .INP files to each other, store them on file shares, run them on air-gapped utility networks. A single HTML file fits that workflow — you can attach it to a LinkedIn post, email it, put it on a USB drive.

2. **Permanence**: A single HTML file works without internet, doesn't depend on CDNs, and won't break when some package gets renamed or unpublished. SWMM5 itself has been stable since 2004 — its companion tools should match that durability.

3. **Auditability**: Anyone with a text editor can inspect the entire codebase. No transpilation, no minification, no source maps. The Cash-Karp Butcher tableau in the code matches the EPA `odesolve.c` source line for line.

4. **Educational**: Students reading the code see the full picture in context. The animation logic, the RK5 solver, and the rainfall generator all live in the same file.

The tradeoff is that the file is larger than a modular version would be. ~150KB minified isn't the file your build pipeline would produce, but it's the file you can `cat` and understand.

---

## Tested File Inputs

### `.RPT` (text report)
The parser extracts the **Subcatchment Runoff Summary** table (precip, runon, evap, infil, runoff, peak runoff, runoff coefficient) and the **Flow Routing Continuity** block. Tested against EPA SWMM5 5.2.0–5.2.4 RPT output.

### `.OUT` (binary results)
The parser reads the documented EPA binary format:
- 28-byte header (magic 516114522, version, flow units, object counts)
- 24-byte tail (section pointers, period count, error code)
- Length-prefixed object IDs
- Property section (skipped via offset)
- Variable lists (subcatchment / node / link / system)
- Time-period results (REAL64 date + REAL32 values per object per variable)

Tested with `flowUnits=CFS`, all eight subcatchment variables (rainfall through soil moisture), six node variables, five link variables, on files from 1 KB to 100+ MB.

---

## Browser Compatibility

| Browser | Version | Status |
|---------|---------|--------|
| Chrome / Edge | 100+ | ✅ Fully supported |
| Firefox | 100+ | ✅ Fully supported |
| Safari | 15+ | ✅ Fully supported |
| Mobile Safari (iOS) | 15+ | ✅ Works (best on tablet) |
| Chrome (Android) | 100+ | ✅ Works (best on tablet) |
| IE 11 | — | ❌ Not supported (uses ES6+) |

Uses modern web APIs: `FileReader`, `DataView`, `Float32Array`, `requestAnimationFrame`, `URL.createObjectURL`. No external dependencies, no polyfills.

---

## Roadmap

### Near-term
- [ ] Force Mains tab — H-W → Manning's n conversion (the `forcemain.c` formula)
- [ ] Anderson Acceleration prototype for SWMM6 dynamic wave solver
- [ ] LID controls (bioretention, permeable pavement, green roof)
- [ ] Multi-subcatchment networks with conveyance routing

### Long-term
- [ ] WebAssembly build of EPA SWMM5 for true engine-in-browser execution
- [ ] SWMM5+ (Ben Hodges' Fortran 2008 finite-volume engine) integration
- [ ] PCSWMM / XPSWMM .inp converter

---

## How to Cite

If you use this tool in academic work, please cite as:

```bibtex
@software{dickinson_swmm5_explorer_2026,
  author       = {Dickinson, Robert E.},
  title        = {SWMM5 Runoff Engine Explorer: Interactive
                  exploration of the EPA SWMM5 runoff engine},
  year         = {2026},
  url          = {https://github.com/SWMMBobSWMM6/swmm5-runoff-explorer},
  note         = {Cash-Karp RK5 ODE solver with three-surface
                  decomposition, in-browser implementation}
}
```

Plain-text citation:

> Dickinson, R.E. (2026). *SWMM5 Runoff Engine Explorer: Interactive exploration of the EPA SWMM5 runoff engine.* https://github.com/SWMMBobSWMM6/swmm5-runoff-explorer

### Related References

- Rossman, L.A. & Huber, W.C. (2016). *Storm Water Management Model Reference Manual Volume I — Hydrology (Revised).* EPA/600/R-15/162A.
- Rossman, L.A. (2017). *Storm Water Management Model Reference Manual Volume II — Hydraulics.* EPA/600/R-17/111.
- Cash, J.R. & Karp, A.H. (1990). *A variable order Runge-Kutta method for initial value problems with rapidly varying right-hand sides.* ACM Transactions on Mathematical Software, 16(3), 201–222.
- Huber, W.C. & Dickinson, R.E. (1988). *Storm Water Management Model Version 4 User's Manual.* EPA/600/3-88/001a.
- Engman, E.T. (1986). *Roughness coefficients for routing surface runoff.* Journal of Irrigation and Drainage Engineering, 112(1), 39–53.

---

## Contributing

This is a public-domain solo project, but contributions are welcome:

- **Bug reports**: open an issue with the storm parameters, infiltration model, and what you expected vs. what you got
- **Feature requests**: open an issue with a clear use case
- **Code contributions**: fork, modify the single HTML file, submit a PR

Before contributing code, please ensure:
1. The single-file architecture is preserved (no build steps, no external dependencies)
2. Self-test suite still passes (click the ✅ Self-Test button)
3. The Cash-Karp coefficients in `odesolve.c` are not modified

---

## License

This work is dedicated to the public domain via [The Unlicense](https://unlicense.org/).

EPA SWMM5 itself is also public domain. This project is in the same spirit.

You are free to:
- Use commercially
- Modify
- Distribute
- Sublicense
- Sell copies
- Use without attribution (though attribution is appreciated)

Without warranty of any kind.

---

## Author

**Robert E. Dickinson**

- 🌐 [swmm5.org](https://swmm5.org) — 1,700+ blog posts on SWMM hydrology and hydraulics
- 📺 [@SWMM5 on YouTube](https://www.youtube.com/@SWMM5) — video tutorials
- 💼 [LinkedIn](https://www.linkedin.com/in/dickinsonre/) — water engineering newsletter
- 🐦 [@SWMM5 on X](https://x.com/SWMM5)
- 💻 [GitHub: @dickinsonre](https://github.com/dickinsonre)

50+ years of continuous SWMM development experience spanning versions 3, 4, 5, and 5+. Co-developer of SWMM3 and SWMM4 at the University of Florida and CDM. SVP at XP Software 1992–1999, where I designed the .xp and .xpx file formats. Visual SWMM, Visual Hydro, Visual Culvert, Visual Inlets at CAiCE Software (acquired by Autodesk 2002). Product Sector Leader for InfoSWMM and InfoSewer at Innovyze (2007–2025). Currently Autodesk Water Technologist transitioning to Expert Elite status. Chair of the SWMM5+ Technical Advisory Committee at CIMM.org. Member of the EWRI Stormwater Modeling Committee.

---

## Acknowledgments

- **EPA Office of Research and Development** — for keeping SWMM5 open source and public domain
- **Wayne Huber** — coauthor of SWMM4 User's Manual, mentor across decades
- **Larry Roesner** — coauthor on SWMM4 EXTRAN module
- **Lewis Rossman** — principal architect of SWMM5
- **Ben Hodges** — for the SWMM5+ Fortran finite-volume engine that's pushing the next generation
- **Jim Cash & Alan Karp** — for the embedded RK5 method that makes this all numerically tractable

---

*"Fifty years in, the engine still has surprises in it. That's what makes it worth understanding."*
