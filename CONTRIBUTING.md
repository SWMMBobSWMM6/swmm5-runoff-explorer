# Contributing

Thanks for your interest in the SWMM5 Runoff Engine Explorer.

## Quick Guidelines

This is a public-domain project. Anyone can fork, modify, redistribute, sell, or relicense without asking. That said, here's how to contribute back:

### Bug Reports

Open an issue with:

1. **Browser & OS** (e.g., Chrome 124 on Windows 11)
2. **What you did** — exact storm parameters, infiltration model, slider values
3. **What you expected** — the result you thought you'd see
4. **What happened** — the actual result, ideally with a screenshot
5. **Console errors** — open DevTools (F12) and copy any red text

If you can attach the `.INP` file the app exported, that helps a lot.

### Feature Requests

Open an issue describing the problem you're trying to solve, why the existing features don't address it, and roughly what UX you'd want.

The bar for new features is high — every addition risks bloating the single-file architecture.

### Code Contributions

Fork the repo, make your changes to `index.html`, submit a PR. Before submitting:

1. **Test in three browsers** — Chrome, Firefox, Safari
2. **Run the self-test suite** — click Self-Test, all 5 tests must pass
3. **Verify .INP export still works** — generate one, run it in EPA SWMM5
4. **Verify .RPT comparison still works** — drag in a real EPA RPT file
5. **Verify .OUT binary reader still works** — drag in a real EPA OUT file
6. **Check file size** — single file should stay under 250KB

### Architecture Constraints

These are non-negotiable:

1. **Single HTML file** — all CSS and JS inline
2. **No build step** — `git clone` then `open index.html` must work
3. **No external runtime dependencies** — no React, no jQuery, no Tailwind
4. **Public-domain code only** — no GPL, MIT, or Apache code that forces a license change

### Ethics

- Don't add tracking, analytics, or telemetry
- Don't add network calls except for documented optional features
- Don't bundle ads or affiliate links
- Don't break SWMM5 compatibility for the sake of cleaner code

---

Thanks for reading. Looking forward to your contributions.

— Robert E. Dickinson
