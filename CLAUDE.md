# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Teaching material for the Alphabit Club (ESI-SBA) workshop "Data Analysis & Advanced Machine Learning" (17 Nov 2026). There is no app, build system, linter or test suite. The repo is a fake cash-on-delivery orders dataset plus a Jupyter notebook and a slide deck. The notebook trains the **same XGBoost model twice**: on crude data (Ch. 2) and after cleaning, exploring and feature engineering (Ch. 6). The workshop teaches **one** metric, recall (the share of real returns the model catches), and the goal is that it improves from the first model to the final one. Don't hardcode score values (F1 etc.) in slides, README or notebook text: they vary with library versions, so use qualitative wording or TP/FP/FN/TN labels. Predicting `returned` (~15% positive) is a binary classification task.

## Repo layout

The git repo has no commits yet (everything is untracked). Current root contents:

- `workshop_notebook.ipynb`: the only notebook (student version, outputs cleared). The solved/presenter notebook and the project zip were removed on purpose. Patch the notebook JSON in place.
- `slides/index.html`: the deck (single offline HTML file)
- `data/orders_raw.csv`: the input. `orders_clean.csv` and `orders_features.csv` are written by the notebook (Ch. 3 and Ch. 5)
- `requirements.txt`: installed by the first code cell of the notebook (`%pip install -q -r requirements.txt`, so the file must sit next to the notebook)
- The data generator (`generate_data.py`), `build_notebook.py` and the old README are no longer in the repo. Copies of the removed zip and solved notebook were moved to the session scratchpad only.

## Commands

- Dependencies: `pip install -r requirements.txt` (the notebook's first code cell does it). Ch. 6 uses `scale_pos_weight`, so there is no imbalanced-learn.

## Architecture

**Data (generator no longer in the repo).** The fake dataset was made in stages: reference tables (wilayas, categories, each with a hidden return-effect and planted spelling variants), customers, orders, then a target from a logistic "return score" with planted rules (night orders, long deliveries, unconfirmed phone, risky wilayas/categories, plus fee/price ratio, first order and quick re-order that the notebook no longer engineers). Then problems were planted: duplicates, typo variants, prices as text like `"2 500 DA"`, 3 date formats, impossible quantities and ages, and NaNs. If you change the notebook story, keep the slides in agreement.

**Notebooks (chapter flow).** Ch. 0 getting started (notebook basics, pandas inspection, problem framing), Ch. 1 why models need numbers (one-hot), Ch. 2 baseline, Ch. 3 cleaning, Ch. 4 exploration (two "discoveries": order_hour, delivery_days), Ch. 5 feature engineering, Ch. 6 final model (class weight via scale_pos_weight, comparison; SMOTE only explained in the slides).
- Everything runs in one session: only Chapter 4 has a **Checkpoint** cell (it reloads `orders_clean.csv`). Chapters 5 and 6 reuse `df` and `final` from earlier cells, and there is no late-joiner block. Chapters 3 and 5 still save `orders_clean.csv` and `orders_features.csv`.
- Data is read straight from the `data/` folder with `pd.read_csv("data/<file>.csv")` (no helper, no GitHub). Students need the `data/` folder next to the notebook (in Colab: upload it). Chapters 3 and 5 write `data/orders_clean.csv` and `data/orders_features.csv`.
- The student notebook has one `TODO` cell with a blank (Ch. 3, `____`). There is no solved version any more.
- In the notebook, the empty cells in Chapter 0 (head, shape, info, describe, value_counts) are intentionally **empty code cells**: the presenter types the pandas code live.
- Markdown cells are deliberately minimal (simple English, a description or `**Expect:**` line only when necessary, i.e. when it explains a concept the student can't guess; the presenter explains live): students are scared by walls of text. Keep new text short, and do not number small titles: only chapter titles are numbered. Keep the `**Expect:**` lines in sync with real outputs when changing code or data.

**Slides** (`slides/index.html`) are a single offline HTML deck (Chapters 0 to 6 are adapted to the notebook: concept visual first, then the pandas command in a `.code` box; slide 2 gives the GitHub repo link (clone or download ZIP); the "what is a model" and "generalize" slides sit right after the workflow slide in Chapter 0 and replaced the old Chapter 2 overfitting slide; the old confusion-matrix and precision/recall/F1 slides are gone), with step-by-step reveals and slide anchors like `#s12`. They are referenced from the notebook text.

## Known open items

- Slides still hardcode data-dependent numbers (6,784 train rows, 88 columns, 42 features, 7,874 rows, 5,385/914 classes, 5.7× weight, pivot table values). The notebook says about 7,880 rows and about 45 columns, so they disagree.
- No notebook cell has been run to confirm the final model beats the first on all three scores.
