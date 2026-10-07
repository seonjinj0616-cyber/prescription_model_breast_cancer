# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

`prescription_model_breast_cancer`: a team Python project for a breast cancer prescription model. The repository is at an early stage. Source code lives in `script/`. There is no dependency file, test suite, or lint configuration yet.

Scripts (run from the `team_project` conda env):

- `script/tcga_brca_download.py`: downloads TCGA-BRCA clinical files (GDC BCR Biotab) and cBioPortal PanCancer Atlas clinical, mutation, and biomarker-gene CNA data into `data/tcga_brca/`. Already-downloaded files are skipped.
- `script/tcga_brca_explore.py`: prints a structure summary and writes `data/tcga_brca/structure_report.md`. Needs `tabulate`.
- `script/tcga_brca_merge.py`: joins all tables on patient ID into one row per patient, `data/tcga_brca/merged/tcga_brca_merged.csv`, plus a column dictionary CSV. Multi-row tables (drugs, radiation, recurrence, follow-up, other cancers, mutations) are summarized per patient. Column prefixes name the source. Run after the download script. Drug names and therapy types are corrected with `dictionaries/drug_names.csv` (raw name to generic) and `dictionaries/drug_classes.csv` (generic to class). The merge stops if a drug name is missing from the dictionary; add it there. Original values stay in `drug_*_raw` columns, and every correction is logged in `merged/drug_corrections.csv`.
- `script/tcga_brca_crop.py`: reads the merged CSV (unchanged) and writes `merged/tcga_braca_merging_cropped.csv` (the user chose this file name), dropping male patients and the columns listed in its `DROP` dict. It also writes a column description txt and a change log txt. Every merged column must be in either `DROP` or `DESCRIPTIONS`, or the script stops. Run after the merge script.
- `metadata/` holds a TCGA-BRCA diagnostic slide list from GDC (v0.2). The script that made it was removed; rewrite it when v0.2 starts.

GDC Biotab files have three header rows (names, alternate names, CDE IDs). Missing values are bracket codes. Keep them: `[Not Available]` means the value was not recorded (maps to 기록 없음), and `[Not Evaluated]` means the test was not done (maps to 미시행).

The planned design is in `PROJECT.md` (Korean, design v0.1). Read it before starting work. Summary:

1. Input module: de-identifies pasted chart text, then a local LLM extracts fields with source quotes.
2. Biomarker module: deterministic rules turn reports into status values (e.g. HER2-low). The LLM cannot change them.
3. RAG over ESMO and ASCO guidelines plus HIRA reimbursement notices, filtered by setting, subtype, and line.
4. A local LLM proposes therapy candidates with a reimbursement class, citing chunk ids for every sentence.
5. A reimbursement auto-update module overlays new HIRA notices on the booklet by effective date.
6. An H&E QC module (H-optimus-0 features from TCGA slides) is deferred to v0.2. Work on v0.1 first, starting with TCGA-BRCA clinical and mutation data, not slides.

This is a non-profit project. ESMO guidelines are used under ESMO's non-commercial terms. Do not use NCCN, NICE, or Korean Breast Cancer Society guideline content. Do not use pathology models that need manual access approval (TITAN, UNI, UNI2-h, CONCH, Virchow2). Patient chart text never goes to an external API; only local models see it. Every output item must cite its source document, page or section, and date. Update `PROJECT.md` when items in its "남은 과제" section are decided.

## Environment

- Use the conda env `team_project` (`C:\Users\301-10\anaconda3\envs\team_project\python.exe`, Python 3.14). Install packages with `conda install -n team_project ...`, not bare `pip`, which may target `base`. A separate uv-installed Python 3.14 (`C:\Users\301-10\.local\bin\python3.14.exe`) also exists; do not use it.
- Platform is Windows. The user communicates in Korean.

## Repository conventions (from .gitignore)

- Large data goes in `data/` and is not committed. Do not commit `*.h5`, `*.db`, `*.parquet`, or `*.npy` files.
- Secrets and API keys go in `.env`, which is ignored.
- A local virtualenv, if created, should be named `.venv/` (ignored).

## Notes

- `README.md` is saved as UTF-16. Read it with an encoding-aware tool rather than plain `cat`.
- Update this file when a dependency file, test runner, or entry point is added.
