# Reproducibility package

Code and result tables for the manuscript "Pipeline choices manufacture
nominally significant directional claims in river discharge–nutrient
monitoring data — an audit with a six-item reporting checklist"
(reproducibility package for the manuscript).

## What runs from this package

- **Figures.** `figures/make_manuscript_figs.py` regenerates every manuscript
  figure (600 dpi TIFF/EPS/PNG/PDF) from the shipped result tables and the
  two processed lake series under `data/processed/`. No external downloads
  are needed.
- **Table checks.** Every number in the manuscript traces to a table under
  `data/results/tables/`; the table-to-script map below names the producer
  of each, or labels it frozen/superseded where the package ships it without
  a producing script.
- **Lake analyses.** `code/lakes_null.py` and `code/taihu_piecewise.py` run
  from `data/processed/` as well (Poyang's permutation null is enumerated
  exactly over all 7! = 5,040 year orders; Taihu uses 1,000 draws plus a
  three-seed sweep).

## What needs the external archives

`code/pipeline.py`, `code/te.py`, `code/strict_layer.py`, `code/shifts_naive.py`,
`code/te_naive_alignment.py`, `code/ccm_nulls.py`, `code/quick_checks.py`,
`code/robustness.py`, `code/cluster_robust.py`, `code/geo_robustness.py`,
`code/interp_halfrl.py`, `code/mixing_counts.py`, `code/ladder.py`,
`code/mccm_grid.py` and `code/mccm_naive_true.py` reconstruct the result
tables from source data. Set `PAPER_BASE` to a directory with:

- `data/raw/` - the public source archives: UNEP GEMS/Water v3 (Zenodo
  concept DOI 10.5281/zenodo.13881899), GSIM indices (PANGAEA
  10.1594/PANGAEA.887477, 10.1594/PANGAEA.887470), the Poyang water-level
  file (Figshare 10.6084/m9.figshare.30913100) and the TPDC Yangtze
  water-quality files (10.11888/Terre.tpdc.303497, 10.11888/Terre.tpdc.303501),
- `data/interim/` - the station-matching tables, shipped here (see below),
- `data/processed/` - the two lake series (also shipped here),
- `data/results/tables/` - where tables are written.

The shipped `data/interim/` tables freeze the station-matching stage:
`matched_river_stations_50km_10yr.csv` pairs GEMStat sampling stations to
GSIM gauges by great-circle distance (<= 50 km) with at least ten years of
overlapping monthly record, and `ccm_qualified_stations*.csv` carry the
qualification gate applied on top (record length and species-code rules, the
v2 file being the gate the corrected pipeline uses). They are deterministic
derivatives of the public archives, shipped so the sample construction is
inspectable without re-deriving it.

Those raw inputs are not redistributed here; 50 of the 60 shipped tables are
the frozen outputs of the current scripts in `code/` and 10 are frozen baseline
or superseded records (all explicitly labeled in the producer map below), so
every table is verifiable without downloading anything.

## Layout

- `code/` - the production analysis scripts.
- `code/archive/` - baseline comparison and reference scripts kept for
  completeness (synthetic zero-coupling generator `mccm_synth.py` imported by
  `mccm_grid.py`, plus baseline null and figure comparison routines).
- `data/results/tables/` - one CSV per analysis (producer map below).
- `data/processed/` - the Poyang and Taihu series used by the lake scripts
  and Figure 6.
- `reporting_template.md` - a filled reporting-checklist template (the
  manuscript's Section 4.2 items, with this package's own values as example
  rows).
- `figures/` - the manuscript figure scripts (`make_manuscript_figs.py` and
  `figstyle.py`).

## Methodological Design Notes and Baseline Comparisons (shipped side by side)

1. **M-CCM cross-mapping orientation convention.** Under Sugihara et al. (2012),
   cross-mapping from the water-quality shadow manifold to discharge
   (`WQ_manifold -> flow_target`) tests `flow->WQ` (`mccm_fixed.csv`, 85.9%
   at `ahead = 1`, $n = 313$; manuscript Section 2.5 and Supplementary Note S2).
   For completeness, the complementary orientation table
   (`mccm_naive_labels_swapped.csv`, 14.1%) and the `ahead = 0` comparison table
   (`mccm_naive_true.csv`) are archived alongside `mccm_fixed.csv`.
2. **Lake surrogate hierarchy.** Section 3.4 contrasts the resampled 200-draw
   11-shift baseline (`dual_lake.csv` and `lakes_backup.csv`) against the
   alignment-calibrated year-block permutation null with in-draw parameter
   re-selection (`lakes_null.csv`, exact $7! = 5,040$ orders for Poyang Lake
   and $N = 1,000$ permutations across seeds 42–44 for Lake Taihu).
3. **Uncalibrated TE alignment null.** `te_alignments_naive_rerun.csv`
   evaluates the 11 whole-year shifts under the exact convention of the
   uncalibrated TE headline statistic (42.2% anchor), archived alongside the
   pairwise baseline (`naive_null.csv`).
4. **KSG-1 estimator specification.** The calibrated TE and conditional TE
   workflows in `strict_layer.py` use a single KSG-1 definition: maximum-norm
   ($L_\infty$) joint distance with strict (`<`) marginal counting (Kraskov et
   al., 2004). `ksg_boundary_check.csv` reports both strict (`<`) and boundary
   (`<=`) marginal counting variants side by side across all 316 combinations.
5. **Whole-year-shift provenance and seam handling.** The 11-shift surrogate
   nulls rotate complete-year blocks of the processed flow series and its
   observation mask by $k \in \{1, \dots, 11\}$ years while holding trailing
   partial years in place, preserving exact calendar-month phase at any record
   length. Each rotated flow mask is intersected with the water-quality mask
   under the two-month short-gap rule and with the rotated usable-month mask
   (`pipeline.rotated_support`), removing 20 rotation-seam slot views out of
   671,147 positional views (`rotation_seam_audit.csv`). Wherever the
   complete-year count divides $k$, the rotation is an exact identity and
   reproduces the observed row (`code/selfcheck.py`).
6. **Concordance permutation bands.** `batch1_pairwise_kappa.csv`,
   `batch1_concperm.csv`, and `batch1_concordance.csv` report pairwise Cohen's
   $\kappa$, five-way Fleiss' $\kappa$, and 5,000-draw margin-preserving
   station-block permutation bands across the 313 tie-free series.
7. **Lake Taihu piecewise detrending.** `taihu_piecewise.csv` (matching
   `Supplementary_taihu_piecewise_detrend.csv`) joins the 2009 breakpoint
   piecewise-detrended CCM skills and argmax-matched permutation $p$-values
   onto the lake-direction table.

## Table-to-script map

In the map below: a row written by a current script is regenerable with the
archives in place; a row labelled *frozen* is a shipped input of the current
producers that this package cannot regenerate; a row labelled *superseded*
is kept for the record only.

| Table | Produced by |
|---|---|
| `combos.csv`, `std_ccm.csv`, `deseason.csv`, `seasonal_r2.csv`, `mccm_naive_labels_swapped.csv` | `pipeline.py` |
| `te_z.csv`, `te.csv`, `ksg_boundary_check.csv`, `strict_nulls.csv`, `strict_months.csv`, `strict_months_null.csv`, `te_loco_strict.csv`, `cluster_robust_strict.csv` | `strict_layer.py` |
| `ablation_ladder_te_z.csv`, `transfer_entropy_491.csv`, `scale_invariance_checks.csv` | `te.py`  (the corrected detrended std-CCM variant share, 51.9%, lives in the C2-row note) |
| `ablation_ladder.csv` | `ladder.py` |
| `null_alignments_naive.csv` | `shifts_naive.py` |
| `te_alignments_naive_rerun.csv` | `te_naive_alignment.py` |
| `ccm_shifts_corrected.csv`, `ccm_shifts_n.csv` | `ccm_nulls.py` (the companion table carries each draw's sample size; draws below the 40-pair minimum are recorded as NaN skills and excluded from the draw's share) |
| `mccm_fixed.csv` | `mccm_fixed.py` (standard Sugihara et al. 2012 orientation applied to `mccm_naive_labels_swapped.csv`; Section 2.5) |
| `synth_grid.csv`, `synth_mixture.csv` | `mccm_grid.py` (`synth_mixture.csv`'s observed CI is the synthetic-layer bootstrap, distinct from the station-cluster CI quoted in the manuscript) |
| `mccm_naive_true.csv` | `mccm_naive_true.py` |
| `batch1_libshare.csv`, `batch1_selfflip.csv`, `batch1_flips.csv`, `batch1_concordance.csv`, `batch1_funnel.csv`, `batch1_te_loco.csv`, `batch1_concperm.csv`, `batch1_pairwise_kappa.csv` | `quick_checks.py` |
| `batch2_esweep.csv`, `batch2_aheadscan.csv`, `batch2_strict.csv`, `batch2_cq.csv` | `robustness.py` |
| `cluster_robust.csv`, `cluster_robust_mccm_fixed.csv` | `cluster_robust.py` |
| `geo_robustness.csv` | `geo_robustness.py` |
| `lakes_null.csv`, `dual_lake.csv` | `lakes_null.py`, `lakes.py` (the manuscript reads only `lakes_null.csv`; `dual_lake.csv` is the superseded-design record) |
| `taihu_trend.csv`, `taihu_descriptives.csv` | `taihu_trend.py` (the descriptives table carries the Section 3.4 Chla–TP correlations and the TLI before-2019 vs 2019–2020 comparison) |
| `null_robustness.csv` | `null_robustness.py` (identity-draw exclusion, record-length strata, and library-split seed shares and vote flips for the eleven-shift nulls; the `n_unit` column states whether `n` counts draws or combinations) |
| `rotation_seam_audit.csv` | `null_robustness.py` (the months the provenance intersection removes at the rotation seam and the trailing-block junction; needs the archives) |
| `site_geometry.csv` | `site_geometry.py` (gauge-to-sampling separation record; needs the archives. The manuscript's 90th percentile moved from 12.2 to 12.0 km when this table pinned the linear-interpolation convention, and the candidate-stage median of 15.3 km is its `candidate_separation_median_km` row) |
| `taihu_piecewise.csv` | `taihu_piecewise.py` |
| `tn_aggregation_sensitivity.csv`, `deseasonalized_sametime_full.csv`, `true_xmap_sametime_full.csv`, `ccm_analysis_results.csv` | frozen from the original naive-layer runs (the row-index sampling ledger and its variants); kept as the naive-layer record and not regenerable from this package |
| `transfer_entropy.csv` | frozen from the original naive-layer conditional-TE run (272 combinations, n_months >= 60); read by `te.py` (step-4 cross-reference) and `ladder.py`; not regenerable from this package |
| `interp_and_halfrl.csv` | `interp_halfrl.py` (half-RL stage needs the archives) |
| `mixing_counts.csv` | `mixing_counts.py` (needs the archives) |
| `calendar_scan.csv` | frozen calendar-gap diagnostic consumed by `interp_halfrl.py` (not regenerable from this package; one `combos` entry is absent from the scan and one scanned entry lies outside the final sample -- `interp_halfrl.py` recomputes the missing entry from the archives and records both in `interp_and_halfrl.csv`) |
| `naive_null.csv`, `lakes_backup.csv`, `lakes_null_discharge_cal.csv`, `mccm_synthetic.csv` | superseded builds kept for the record, documented above (no producing script in this package) |

## Manuscript number -> table index (headline numbers)

| Manuscript number | Table |
|---|---|
| naive shares 60.1 / 47.5 / 63.1 / 42.2 / 67.0 | `ablation_ladder.csv`, `mccm_naive_true.csv`, `te_alignments_naive_rerun.csv`, `transfer_entropy_491.csv` |
| naive eleven-shift nulls (CCM and TE) | `null_alignments_naive.csv`, `te_alignments_naive_rerun.csv` |
| corrected CCM 51.9 / deseasonalized 46.8 and their own nulls | `std_ccm.csv`, `deseason.csv`, `ccm_shifts_corrected.csv`, `ccm_shifts_n.csv` |
| corrected M-CCM 85.9 (n = 313) | `mccm_fixed.csv`, `cluster_robust_mccm_fixed.csv` |
| TE / conditional TE (strict counting) and their nulls | `te_z.csv`, `strict_nulls.csv`, `ksg_boundary_check.csv` |
| TE cluster intervals (strict and unstandardized) | `cluster_robust_strict.csv` |
| strict-months TE and its own null | `strict_months.csv`, `strict_months_null.csv` |
| unstandardized TE share and zeros | `te.csv` |
| dose-response grid, mixture expectation | `synth_grid.csv`, `synth_mixture.csv` |
| concordance point estimates, bands and flips | `batch1_pairwise_kappa.csv`, `batch1_concperm.csv`, `batch1_concordance.csv` |
| per-country and leave-one-out scopes (all four methods) | `geo_robustness.csv`, `te_loco_strict.csv` |
| lake directions, three nulls each | `lakes_null.csv`; Taihu piecewise `taihu_piecewise.csv`; Section 3.4 structural descriptives `taihu_descriptives.csv` |
| Section 2.7 null robustness (identity exclusion, strata, split seeds and vote flips, rotation-seam audit) | `null_robustness.csv`, `rotation_seam_audit.csv` |
| gauge-to-sampling geometry sentences (median, 90th percentile, candidate-stage median) | `site_geometry.csv` |
| robustness batch (E x tau, ahead scan, library, c-Q) | `batch2_esweep.csv`, `batch2_aheadscan.csv`, `batch2_strict.csv`, `batch2_cq.csv` |
| funnel, species-mixing screen | `batch1_funnel.csv`, `mixing_counts.csv` |
| interpolation footprint, half-RL share | `interp_and_halfrl.csv` |

## Running

Full rebuild from the archives, in dependency order (producers as in the
map above; every command writes its tables under `data/results/tables/`):

    export PAPER_BASE=/path/to/project
    python3 code/pipeline.py            # combos, standard CCM, deseasonalized, seasonal r2, naive labels
    python3 code/te.py                  # standardized TE layer + ablation rungs
    python3 code/strict_layer.py        # corrected TE/CTE shares, shift nulls, strict-months check, LOCO, cluster CIs
    python3 code/ladder.py              # ablation ladder (reads the te.py outputs)
    python3 code/mccm_grid.py           # synthetic dose-response grid and mixture expectation
    python3 code/mccm_naive_true.py     # naive M-CCM share
    python3 code/mccm_fixed.py          # corrected M-CCM share (label convention; reads shipped tables only)
    python3 code/quick_checks.py        # concordance batch (reads the corrected votes)
    python3 code/robustness.py          # E x tau sweep, ahead scan, library fraction, c-Q
    python3 code/cluster_robust.py
    python3 code/geo_robustness.py
    python3 code/ccm_nulls.py           # corrected CCM own shift nulls
    python3 code/shifts_naive.py        # naive CCM shift null + power block
    python3 code/te_naive_alignment.py  # naive TE shift null (42.2% anchor gate)
    python3 code/site_geometry.py       # gauge-to-sampling separation record
    python3 code/null_robustness.py     # identity exclusion, strata, split-seed stability
    python3 code/interp_halfrl.py       # interpolation footprint (half-RL stage)
    python3 code/mixing_counts.py       # species-mixing screen
    python3 code/selfcheck.py           # month-phase certificate, table keys, producer map, identity shifts, null-value integrity

Lake analyses and the figures run from the shipped inputs alone, in any
order relative to the block above:

    python3 code/lakes_null.py && python3 code/lakes.py
    python3 code/taihu_trend.py && python3 code/taihu_piecewise.py
    cd figures && PAPER_BASE=$PWD/.. python3 make_manuscript_figs.py

Minimal verification without the archives:

    PAPER_BASE=$PWD python3 code/selfcheck.py   # five of the six checks; the six-check
                                                 # certificate needs data/raw for the archive cross-check

(`selfcheck.py` runs six checks. Two are the month-phase certificate: the pure
operator test over the 166 shipped calendar lengths, which runs from the package
alone, and the archive month-vector cross-check, which re-reads the real
calendars when the archives are supplied and also asserts that every analysed
draw value comes from an observed month or a gap of at most two months of the
base record (never a long-gap interpolation). Four are table checks that run
from the package alone: key integrity (complete keys and anchor rows, strict
draws complete, reduced month-draw coverage printed), producer integrity (every
result table written by exactly one current script or labelled frozen/
superseded, zero undeclared), identity exactness (every identity rotation
reproduces the observed row), and null-value integrity (all values finite; NaN
CCM draws matched to sub-40 sample sizes in the companion table). With the
archives supplied it prints ALL PASS (6 of 6); on the package alone it certifies
five of six, prints the archive cross-check as not executed, and closes with a
partial-certificate line rather than a full pass.)

Python 3.9 with the versions in `requirements.txt`. Every stochastic step
fixes its seed. Tables are written to `data/results/tables/`.
