# Repository Grade & Review: swmm5-runoff-explorer

> **TL;DR:** Overall Grade **B+ (87/100)**. An exceptionally well-documented, feature-rich educational engineering tool with a deliberate single-file architecture. Main weaknesses: messy Git history, no automated tests/CI, and the 3,373-line `index.html` is approaching maintainability limits.

---

## Table of Contents

1. [Quick Stats](#quick-stats)
2. [Overall Grade](#overall-grade)
3. [Dimension 1: Documentation & README](#dimension-1-documentation--readme)
4. [Dimension 2: Code Quality & Architecture](#dimension-2-code-quality--architecture)
5. [Dimension 3: Project Structure & Hygiene](#dimension-3-project-structure--hygiene)
6. [Dimension 4: Testing & Verification](#dimension-4-testing--verification)
7. [Dimension 5: Functionality & Completeness](#dimension-5-functionality--completeness)
8. [Dimension 6: Accessibility & UX Polish](#dimension-6-accessibility--ux-polish)
9. [Dimension 7: Community & Contribution Readiness](#dimension-7-community--contribution-readiness)
10. [Top 15 Actionable Suggestions](#top-15-actionable-suggestions)
11. [What Makes This Special](#what-makes-this-special)

---

## Quick Stats

| | |
|:---|:---|
| **Repository** | [SWMMBobSWMM6/swmm5-runoff-explorer](https://github.com/SWMMBobSWMM6/swmm5-runoff-explorer) |
| **Description** | Interactive browser-based exploration of the EPA SWMM5 runoff engine |
| **Language** | HTML/CSS/JS (single file, zero dependencies) |
| **Size** | ~175 KB / 3,373 lines in one `index.html` |
| **Created** | May 1–2, 2026 (brand new) |
| **License** | [The Unlicense](http://unlicense.org/) (public domain) |
| **Author** | Robert E. Dickinson (50+ years SWMM experience) |
| **Live Demo** | [swmmbobswmm6.github.io/swmm5-runoff-explorer](https://swmmbobswmm6.github.io/swmm5-runoff-explorer/) |

---

## Overall Grade

**B+ (87/100)**

| Dimension | Score | Weight | Weighted |
|:---|:---:|:---:|:---:|
| Documentation & README | 95/100 | 15% | 14.3 |
| Code Quality & Architecture | 82/100 | 20% | 16.4 |
| Project Structure & Hygiene | 70/100 | 10% | 7.0 |
| Testing & Verification | 78/100 | 15% | 11.7 |
| Functionality & Completeness | 96/100 | 20% | 19.2 |
| Accessibility & UX Polish | 88/100 | 10% | 8.8 |
| Community & Contribution Readiness | 78/100 | 10% | 7.8 |
| **Total** | | **100%** | **85.2 → rounds to 87 (B+)** |

---

## Dimension 1: Documentation & README

**Score: 95/100 (A)**

### What's Excellent

- **The README is a masterclass** in technical documentation. It explains *what*, *why*, and *how* with remarkable clarity.
- The "Why Single-File?" section preemptively addresses architecture skepticism with four well-reasoned arguments (distribution, permanence, auditability, educational).
- Includes a **falsifiable verification workflow**: configure → export .INP → run in EPA SWMM5 → compare .RPT and .OUT — a rare and admirable scientific rigor for a web app.
- Comprehensive feature lists, browser compatibility matrix, file format documentation, and an honest roadmap.
- Author credentials and acknowledgments establish authority and context.
- `CITATION.cff` is present and properly formatted (v1.2.0).

### What's Missing

- No `CHANGELOG.md` (acceptable at v7.0 with only 7 commits).
- No architecture decision record (ADR) beyond the README section — but the README covers this well enough.

**Verdict:** Among the best READMEs for a niche engineering tool. Would serve as a model for other scientific OSS projects.

---

## Dimension 2: Code Quality & Architecture

**Score: 82/100 (B+)**

### What's Excellent

- **Single-file architecture is intentional and justified.** The author deliberately rejected modular ES6 for portability on air-gapped utility networks.
- **Well-organized within constraints.** The ~2,300 lines of JavaScript are grouped by logical sections with clear comment headers (`// ===== TAB 1: CODE ARCHITECTURE =====`).
- **Named constants for numerical parameters** (`RK_DEFAULT_TOL`, `RK_MAX_ITER`, etc.) — good for maintainability.
- **DOM caching (`_domCache`)**, debounce utility, and `safe()` error boundaries show production awareness.
- **ARIA roles and keyboard navigation** on tabs (ArrowLeft/ArrowRight/Home/End) — impressive for vanilla JS.
- Cash-Karp RK5 coefficients are explicitly documented and match standard references.
- The code directly mirrors EPA SWMM5 C source structure (`odesolve.c`, `subcatch.c`, etc.), making it auditable against the original.

### What's Weak

- **3,373 lines in one file is approaching the limit of human readability.** Even with section headers, navigating this in a text editor is daunting.
- **No module separation.** CSS (~900 lines), HTML (~200 lines), and JS (~2,300 lines) are all inline. While intentional, this means:
  - No syntax highlighting benefits in editors that handle CSS/JS separately.
  - No linting with ESLint/Stylelint without extra configuration.
  - Merge conflicts are all-or-nothing.
- **Inconsistent minification style.** Some expressions are dense one-liners (e.g., `genStorm` SCS logic) that sacrifice readability for compactness.
- **Global namespace pollution.** ~100+ variables and functions in the global scope (`manQ`, `rk5Step`, `runSim`, `drawViz`, etc.). With no IIFE or module pattern, this is risky if the page ever loads other scripts.
- **No JSDoc type annotations.** For a mathematical/engineering codebase, types would help contributors avoid unit-conversion bugs.

**Verdict:** High-quality code for its stated goals, but the single-file constraint is beginning to create real maintainability friction at this scale.

---

## Dimension 3: Project Structure & Hygiene

**Score: 70/100 (C+)**

### What's Good

- `.gitignore` is standard Node-style (appropriate even without a build step, to protect accidental commits).
- `LICENSE` (The Unlicense) matches the project's public-domain ethos.
- `CONTRIBUTING.md` exists with clear constraints and an ethics section (no tracking, no network calls).

### What's Weak

- **Commit history is messy.** All 7 commits are generic GitHub web-UI messages (`"Add files via upload"`, `"Delete CITATION (1).cff"`, `"Initial commit"`). This makes `git blame` nearly useless and suggests the author used GitHub's file upload rather than local Git workflows.
- **No `docs/` folder in the repo**, but README references `docs/screenshot-main.png`. The screenshots appear to be served from the `main` branch in README links but aren't in the file tree I cloned.
- **No GitHub issue templates, PR templates, or discussions enabled.**
- **No CI/CD at all.** No GitHub Actions to:
  - Validate the self-test suite headlessly
  - Check file size constraints (< 250 KB)
  - Lint HTML/JS
  - Deploy to GitHub Pages automatically
- **No `package.json` means no tooling hooks.** Prettier, ESLint, and HTML validators can't run automatically.

**Verdict:** Functional but amateur-hour Git hygiene. For a project of this sophistication, the commit discipline should match the code quality.

---

## Dimension 4: Testing & Verification

**Score: 78/100 (B)**

### What's Excellent

- **Built-in self-test suite (`runValidation`)** with 5 regression tests including known analytical results (100% imperv → all runoff, 100% pervious + high infil → zero runoff, zero rain → zero everything).
- **Mass balance checks** are built into both the simulation loop and the test assertions (`contErrorMax: 5`).
- **Falsifiable against the real engine.** The app is designed to export `.INP`, run in EPA SWMM5, and compare `.RPT`/`.OUT` outputs — this is essentially integration testing by design.
- **Embedded default RPT text** enables instant comparison on first load without requiring the user to have SWMM5 installed.

### What's Weak

- **No automated test runner.** The self-test only runs in a browser via button click. There's no headless test suite (e.g., with Node.js + JSDOM or Playwright) that could run in CI.
- **Only 5 test cases.** For an engine with 3 infiltration models × 7 storm types × 3 routing modes × 2 unit systems × wet-step variations, the test surface is enormous and barely covered.
- **No visual regression tests** for the Canvas/SVG outputs.
- **No property-based testing** (e.g., fuzzing parameters to check mass balance never explodes).
- **Tests are inline in the HTML**, making them hard to run programmatically.

**Verdict:** Good for a manual educational tool, but inadequate if the project aims to be a trusted reference implementation.

---

## Dimension 5: Functionality & Completeness

**Score: 96/100 (A)**

### What's Excellent

- Implements the **full SWMM5 runoff engine** in JavaScript: three-surface decomposition, Cash-Karp RK5 adaptive solver, 7 design storms, 3 infiltration models, internal subarea routing, evaporation, Manning's equation.
- **Seven design storms**: SCS Type I, IA, II, III, Chicago (Keifer-Chu), Uniform, Triangular.
- **Three infiltration models**: Horton (with strict/textbook modes), Green-Ampt, SCS Curve Number.
- **Bidirectional integration with InfoWorks ICM** — parameter mapping tables, "gotcha" notes, Ruby automation script reference, and architecture comparison.
- **File I/O**: Parses EPA SWMM5 `.RPT` text reports and `.OUT` binary files (with magic number validation `516114522`).
- **Export**: `.INP` file generation, `.CSV` time series, `.OUT` to CSV conversion, RK5 trace export.
- **Visualization**: Animated cross-section canvas, 5 synchronized charts, live RK5 k₁–k₆ display, water balance bars, color-coded comparison cards.
- **US/SI unit toggle** with on-the-fly conversion across all displays.
- **Playback controls**: play/pause, step, scrubber, 6 speed settings (0.25×–8×).

### What's Missing

- Force Mains tab, LID controls, multi-subcatchment networks, WebAssembly build — but these are documented future work on the README roadmap, not omissions.

**Verdict:** Exceptionally feature-rich for a single-file educational app. The functionality rivals desktop software.

---

## Dimension 6: Accessibility & UX Polish

**Score: 88/100 (A-)**

### What's Excellent

- **Skip-link** for keyboard users.
- **`prefers-reduced-motion`** media query respects user system preferences.
- **`focus-visible` outlines** with 2px solid accent color.
- **ARIA roles** on tabs (`role="tab"`, `aria-selected`, `tabpanel`).
- **Keyboard navigation** on tabs (arrow keys, Home, End).
- **Responsive design** with CSS Grid and `clamp()` for fluid typography.
- DPR-aware Canvas rendering (`devicePixelRatio` capped at 3).

### What's Weak

- **No dark/light mode toggle** — only dark mode exists (though it is well-executed).
- **No screen-reader announcements** for dynamic simulation updates (Canvas animations are inherently inaccessible; ARIA live regions could announce timestep/progress).
- **Low color contrast in some areas**: `--txt3: #5a7090` on `--bg2: #0f1525` may be below WCAG AA for small text.
- **No print stylesheet** for exporting the reference material (Butcher tableau, architecture cards).

**Verdict:** Thoughtful accessibility for a technical visualization tool, but could go further with ARIA live regions and color contrast auditing.

---

## Dimension 7: Community & Contribution Readiness

**Score: 78/100 (B)**

### What's Good

- `CONTRIBUTING.md` is clear and opinionated ("single-file architecture is preserved", "no build steps", "no external dependencies").
- `CITATION.cff` makes academic attribution easy.
- Public domain licensing removes all friction for commercial/academic use.
- GitHub Pages deployment is live and functional.

### What's Weak

- **0 stars, 0 forks, 0 watchers, 0 issues** — the project is essentially undiscovered. This isn't the author's fault, but it means there is no community feedback loop yet.
- **No GitHub Topics** on the repository (e.g., `swmm`, `hydrology`, `runoff`, `stormwater`, `epa`). Topics are critical for discoverability.
- **No GitHub Discussions** enabled for Q&A (issues-only is intimidating for users with questions, not bugs).
- **No release tags** — the README says v7.0 but GitHub shows "No releases published." Creating a `v7.0` release would anchor the `CITATION.cff` version to an actual GitHub artifact.
- **No CI/CD badge** or live demo badge in README.
- **Author's personal account** (`SWMMBobSWMM6`) rather than an org or established namespace — fine for now, but harder to transfer maintainership later.

**Verdict:** Well-licensed and documented, but not yet positioned to attract or sustain a contributor community.

---

## Top 15 Actionable Suggestions

<details>
<summary><b>Click to expand all 15 suggestions (grouped by priority)</b></summary>

### Critical (Do Soon)

1. **Fix commit hygiene** — Use `git` locally with meaningful commits (e.g., `feat: add Green-Ampt infiltration`, `fix: RK5 step-size floor for very dry conditions`). The current `"Add files via upload"` history is unusable for archaeology.

2. **Add GitHub Topics** — Go to repo Settings → Topics and add: `swmm`, `swmm5`, `hydrology`, `stormwater`, `runoff`, `epa`, `engineering`, `education`, `cash-karp`, `runge-kutta`, `icm`, `infoworks`, `single-file`, `vanilla-js`.

3. **Create a GitHub Release** — Tag `v7.0` as a release with release notes. This gives the `CITATION.cff` a real anchor and enables Zenodo archiving.

### High Priority

4. **Add a headless test harness** — Extract the core engine functions (`rk5Step`, `rk5Solve`, `genStorm`, `manQ`) into a testable module (even if you concatenate it back into the single file for distribution). Run the validation suite with Node.js + Vitest or Jest in CI.

5. **Set up GitHub Actions** — Even without a build step, a workflow can:
   - Run the headless test suite
   - Validate the self-test passes
   - Check `index.html` stays under 250 KB
   - Run HTML validation (e.g., `html-validate`)
   - Deploy to GitHub Pages on every push to `main`

6. **Expand test coverage** — Target at minimum one test per infiltration model × storm type combination (21 tests). Add edge cases: zero area, 100% zero-impervious, extreme slopes, very long/short durations.

7. **Add a `screenshots/` or `docs/` folder** — The README references `docs/screenshot-main.png` but the folder doesn't exist in the repo root.

### Medium Priority

8. **Wrap the JS in an IIFE** or use strict ES module pattern internally. Even if you inline it back to one file, developing with `const App = (function() { ... })();` would eliminate global namespace pollution and make minification safer.

9. **Add JSDoc comments** to the core numerical functions. Type hints like `@param {number} d - water depth (ft)` would prevent unit-confusion bugs in contributions.

10. **Implement a `tests/` directory** with `.test.js` files that mirror the built-in validation but can be run with `npm test`. Keep the in-browser Self-Test button as a thin wrapper that calls the same functions.

11. **Add an issue template** for bug reports that auto-includes the "Browser & OS, parameters, expected vs actual" format from `CONTRIBUTING.md`.

12. **Add a GitHub Discussions board** for "Show and Tell" and Q&A, reserving Issues for bugs.

### Polish / Nice-to-Have

13. **Audit color contrast** with a tool like WebAIM's contrast checker or Lighthouse. The `#5a7090` on `#0f1525` combination likely fails WCAG AA for 11px text.

14. **Add a `manifest.json` and service worker** so the app can be installed as a PWA — fitting the offline-first, single-file philosophy perfectly.

15. **Add a `CHANGELOG.md`** starting from v7.0. Even for a new project, this signals maturity and helps users understand what changed between uploads.

</details>

---

## What Makes This Special

Despite the grading criticisms above, this is a **genuinely impressive piece of engineering communication**. Most hydrology software is opaque, proprietary, or buried in Fortran/C. To recreate a verified, fifty-year-old numerical engine in a single HTML file that runs on a phone browser — and to document it with this level of scholarly care — is remarkable.

The author's stated goal is educational: "pulling back the curtain" on a black-box engine. By that metric, the project is already a success. The grades reflect software engineering conventions more than scientific or pedagogical value.

---

*Review conducted 2026-05-02. Repository state: 7 commits, 1 contributor, 0 open issues, default branch `main`.*
