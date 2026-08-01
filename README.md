# Lunar Structural Trend Classifier

A browser-based tool for classifying the orientation of lunar structural features — wrinkle ridges, mare boundaries, and other traced arcs — from [LROC QuickMap](https://quickmap.lroc.im-ldi.com/) profile exports. Computes compass-direction classifications and visualizes regional trends as a live rose diagram, entirely client-side.

![type: single-file HTML/JS](https://img.shields.io/badge/type-single--file%20HTML%2FJS-C97B3D)
![no backend](https://img.shields.io/badge/backend-none-5B8FA8)
![license](https://img.shields.io/badge/license-MIT-8F7FBF)

## Why

LROC QuickMap lets you trace arcs across the lunar surface (ridges, scarps, mare boundaries, etc.) and export the traced path as a CSV of sampled points with elevation and coordinates. That's great raw data — but if you're doing structural analysis across dozens or hundreds of traced features, there's no built-in way to answer the basic question: **what direction does this feature trend, and what's the dominant trend across the region?**

This tool closes that gap. Drop in the CSV exports, and it computes each feature's orientation, classifies it, and builds a rose diagram — the standard way structural geologists visualize trend distributions — as you go.

## Features

- **Bulk CSV upload** — select one or many QuickMap profile exports at once
- **Great-circle bearing calculation** from each feature's start → end coordinates
- **16-point compass classification** (N, NNE, NE, ... NNW)
- **Axial vs. directional mode** — ridges and boundaries are lines, not vectors, so trending NE is the same as trending SW. Axial mode (0–180°) folds these together; directional mode (0–360°) is available if you need true bearing.
- **Live rose diagram**, color-coded and stacked by feature type (Wrinkle Ridge / Mare Boundary / Other), with dominant-trend statistics
- **Per-feature editable tagging** — reclassify or delete individual entries after upload
- **CSV export** of the full classified dataset for downstream analysis or figures
- **Zero dependencies, zero backend** — a single HTML file; all parsing and computation happens in-browser

## Usage

1. Open `lunar_trend_classifier.html` in any modern browser (no install, no server required)
2. Set the **"Tag new uploads as"** dropdown to match the batch you're about to add
3. Click **+ Add CSV(s)** and select your QuickMap profile export(s)
4. Review/edit tags per row, toggle between axial and directional rose diagram modes
5. Click **Export table (.csv)** to download the classified dataset

### Expected input format

CSV files exported from QuickMap's "Draw & Search → elevation profile" tool, with columns:

```
position,TerrainHeight,SLDEM2015 (+ LOLA),lon,lat
0,-2416.08,-2416.072013,10.790009,21.957866
0.20298,-2405.18,-2405.172198,10.795271,21.953285
...
```

Only `lat`, `lon`, and `position` are used. `position` (the cumulative distance along the path, in km) is used to report feature length; the first and last valid `(lat, lon)` rows define the feature's start and end points for bearing calculation.

## Methodology

Bearing is computed using the standard great-circle (initial) bearing formula between the first and last coordinate of each traced path:

```
θ = atan2( sin(Δλ)·cos(φ2), cos(φ1)·sin(φ2) − sin(φ1)·cos(φ2)·cos(Δλ) )
```

where φ is latitude, λ is longitude, and the result is normalized to 0–360°. This is then binned into 16 compass directions (22.5° per bin) and, for axial mode, folded modulo 180°.

**Limitation:** start→end bearing captures overall trend well for mostly-linear features, but will understate local curvature for strongly arcuate traces. A least-squares line-fit across all path points would be a more robust alternative for curvy features — not currently implemented.

## Tech stack

Vanilla HTML/CSS/JS. No frameworks, no build step, no external requests at runtime — CSV parsing and SVG rendering are both hand-rolled to keep the tool fully self-contained and portable.

## Data source

[LROC QuickMap](https://quickmap.lroc.im-ldi.com/) — NASA/ASU Lunar Reconnaissance Orbiter Camera team.

## License

MIT
