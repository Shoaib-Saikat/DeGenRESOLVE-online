# DeGenRESOLVE online

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/Shoaib-Saikat/DeGenRESOLVE-online?quickstart=1)

Run **[DeGenRESOLVE](https://github.com/Shoaib-Saikat/DeGenRESOLVE-ONT-Sequencing-Data-Analyzer)**,
the Oxford Nanopore amplicon consensus pipeline with automated IUPAC ambiguity resolution,
from a web browser. Nothing needs to be installed.

Click the button above, sign in to GitHub and wait 2–3 minutes. You get the real
DeGenRESOLVE desktop app in a browser tab, with the bundled influenza A demo data ready to
run. Instructions open automatically (`WELCOME.md`).

Website: <https://degenresolve.vercel.app>

## Who pays, and where the data goes

- Each person runs DeGenRESOLVE in **their own** GitHub codespace. Usage counts against
  **their** free monthly allowance (120 core-hours on GitHub Free, so about 30 hours on the
  4-core machine this uses). Nothing is billed to the authors.
- Uploaded reads stay inside that person's codespace. Nothing is sent anywhere else.
- Codespaces stop after 30 minutes without activity. Stopped codespaces only use storage.

## What's inside

| Path | Purpose |
|------|---------|
| `.devcontainer/devcontainer.json` | What Codespaces starts: a 4-core / 16 GB machine running the prebuilt image, the desktop on port 6080, and the app started automatically |
| `image/Dockerfile` | DeGenRESOLVE **v1.0.1** (pinned commit) plus its validated conda environment, created from the project's own `env.lock`: samtools/bcftools 1.24, minimap2 2.31, porechop, seqtk, NanoPlot, Qualimap, PyQt5 |
| `image/.devcontainer/` | Adds the `desktop-lite` feature (Fluxbox + TigerVNC + noVNC) at build time |
| `image/bin/` | Helpers: `degenresolve-gui`, `degenresolve-zip`, `degenresolve-compare`, `degenresolve-selftest`, `degenresolve-monitor`, `degenresolve-setup` |
| `.github/workflows/build-image.yml` | Builds the image, **runs the full demo pipeline inside it and checks the output is identical to the v1.0.1 reference run**, then publishes it to `ghcr.io` |
| `site/` | Static landing page, deployed on Vercel |

The pipeline code is not modified. The image runs the published release exactly as the
offline installer would.

## Reproducibility check

Every image is tested before it is published. CI copies the demo inputs into an empty folder
and runs `main_with_config.sh pipeline_config.json`. `degenresolve-compare` then checks the
output against `test_data/results` from the release:

- draft (step 7) and refined (step 8) consensus FASTA, byte for byte, so the case and
  therefore the soft-masking must match too
- every *scope A* checksum in the run receipts: merged and trimmed reads, SAM, BAM and VCF
  records, consensus, and diagnostic log
- the soft-masked fraction (0.0% for barcode01, 60.1% for barcode09)
- tool versions against the release's `bundled_tool_versions.json`

The latest result is on the workflow run's summary page. Inside a codespace you can repeat
it with `degenresolve-selftest`, or check a demo run made in the app with
`degenresolve-compare demo_data`.

## Releasing a new DeGenRESOLVE version

1. In `image/Dockerfile`, set `DEGENRESOLVE_TAG` and `DEGENRESOLVE_COMMIT` to the new release.
2. In `.devcontainer/devcontainer.json`, change the image tag to the new version.
3. Commit to `main`. The workflow builds, tests and publishes `ghcr.io/shoaib-saikat/degenresolve-online:<version>`.

## Licence

MIT, same as DeGenRESOLVE. © 2026 Shoaib Saikat.
