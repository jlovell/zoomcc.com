# zoomcc.com

Static landing page for Zoom Computer Consulting, hosted on GitHub Pages.

Recovered on 2026-09-28 from the Wayback Machine snapshot of 2025-03-19
(https://web.archive.org/web/20250319041646/http://zoomcc.com/) after the
original host (justhost.com) stopped serving the site.

Changes from the archived original:

- Google Fonts stylesheet loaded over https instead of http (required on an https host).
- The contact line, which read `jason@.` in the archived page, now shows the full address as a mailto link.
- A malformed closing `</div class="container">` tag was corrected to `</div>`.

Everything else, including all styling, is byte-for-byte the archived page.

## Deploying

Push to `main`. GitHub Pages serves the repository root. The `CNAME` file pins the
custom domain; `.nojekyll` disables Jekyll processing.
