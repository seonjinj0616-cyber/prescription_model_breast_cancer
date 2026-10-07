# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

`prescription_model_breast_cancer`: a team Python project for a breast cancer prescription model. The repository is at an early stage. Source code lives in `script/`, which currently holds only an empty `a.py`. There is no dependency file, test suite, or lint configuration yet.

The planned design is in `PROJECT.md` (Korean). Read it before starting work. Summary:

1. An image model reads one primary-tumor H&E slide and predicts ER/PR/HER2, histologic grade, and an N0/N+ probability (reference only).
2. User input supplies tumor size, M, positive lymph node count, menopausal status, treatment setting, and line of therapy. Input overrides predictions.
3. A treatment-options module combines HIRA reimbursement rules (rule-coded from the HIRA PDF, not judged by the LLM), MFDS drug approval data, and NCI PDQ (RAG) for rationale.

Do not use NCCN, ESMO, NICE, or Korean Breast Cancer Society guideline content: their terms forbid AI use without permission. Do not use pathology models that need manual access approval (UNI, Virchow2, CONCH). Every output item must cite its source document, page or section, and date. Update `PROJECT.md` when items in its "아직 정하지 않은 사항" section are decided.

## Environment

- Python comes from the Anaconda installation (`C:\Users\301-10\anaconda3`, Python 3.13). No project-specific conda env or `.venv` exists yet.
- Platform is Windows. The user communicates in Korean.

## Repository conventions (from .gitignore)

- Large data goes in `data/` and is not committed. Do not commit `*.h5`, `*.db`, `*.parquet`, or `*.npy` files.
- Secrets and API keys go in `.env`, which is ignored.
- A local virtualenv, if created, should be named `.venv/` (ignored).

## Notes

- `README.md` is saved as UTF-16. Read it with an encoding-aware tool rather than plain `cat`.
- Update this file when a dependency file, test runner, or entry point is added.
