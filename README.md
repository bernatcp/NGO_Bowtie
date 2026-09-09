# NGO_Bowtie

Supplementary code and outputs for an academic paper on a civic-space risk-monitoring pilot for Nepal, built entirely on the Claude API (`claude-sonnet-5`).

The pilot follows a "bow-tie" risk model: for a given threat category, evidence is filed against the barriers that prevent it and the consequences if it occurs. The notebook automates three stages of that workflow end-to-end and produces citation-bound, machine-checkable outputs at each step.

## What it does

`collector_maintainer_pilot_v4_14.ipynb` runs a 3-stage pipeline:

1. **Collector** — searches and fetches real web pages (`web_search` + `web_fetch`) to gather provenance-bearing evidence on Nepal's civic space, and writes it to `collector_run_v4.json`.
2. **Classifier** ("filing stage") — assigns each evidence item to a bow-tie risk taxonomy (threat category, polarity, affected barrier), writing `classification_run_v4.json` and `classified_evidence_v4.csv`.
3. **Maintainer / article writer** — sequentially rewrites a running narrative across three reporting periods (baseline 31 Dec 2024, peak 2025, divergence through June 2026) under a four-state status policy, writing `article_revisions_v4.json`. A final cell renders a summary timeline (`nepal_bowtie_timeline.svg` / `nepal_bowtie_timeline_v4.json`).

Every run with `EVIDENCE_MODE = "live"` starts fresh and fully overwrites all output files — there is no stale-data carryover between runs.

**Scope note (from the notebook itself):** this pilot demonstrates that collection, filing, and sequential article maintenance can be instantiated end-to-end with citation-bound outputs. It does **not** by itself establish production-level source recall, continuous change detection, cross-language performance, status calibration, or expert-level classification accuracy — those require a gold set and human evaluation. Proposed status changes, especially de-escalations, remain subject to human review.

## Repository contents

| File | Produced by | Description |
|---|---|---|
| `collector_maintainer_pilot_v4_14.ipynb` | — | The full pipeline notebook |
| `collector_run_v4.json` | Stage 1 | Raw collected evidence with provenance |
| `classification_run_v4.json` / `classified_evidence_v4.csv` | Stage 2 | Evidence filed against the bow-tie taxonomy |
| `article_revisions_v4.json` | Stage 3 | Sequential narrative revisions across the 3 reporting periods |
| `nepal_bowtie_timeline_v4.json` / `nepal_bowtie_timeline.svg` | Stage 3 | Rendered summary timeline |

These outputs are the artifacts of a specific run and are checked in as the record referenced by the paper.

## Setup

Requires Python 3.10+ and an [Anthropic API key](https://console.anthropic.com/) **scoped to a specific workspace** (a key not scoped to a workspace will fail with a `400` error).

```bash
pip install -r requirements.txt
cp .env.example .env   # then edit .env and set ANTHROPIC_API_KEY
```

## Running

Open `collector_maintainer_pilot_v4_14.ipynb` in Jupyter and run all cells, or execute it headlessly:

```bash
jupyter nbconvert --to notebook --execute --inplace \
  --ExecutePreprocessor.timeout=1800 \
  collector_maintainer_pilot_v4_14.ipynb
```

A full run makes several real Claude API calls and takes roughly 10–20 minutes. Set `EVIDENCE_MODE = "artifact"` in the Configuration section to replay against a previously collected evidence file instead of performing a new live collection.

### Cost

A full live run costs approximately **US$0.80–1.00** at Sonnet 5 pricing ($2 / $10 per 1M input/output tokens), broken down roughly as: collector ~$0.24, classifier ~$0.21, maintainer ~$0.29, timeline render ~$0.08. These are estimates derived from output file token sizes, not exact billed usage.

## License

- The **code** in this repository (the notebook) is licensed under the [MIT License](LICENSE_CODE).
- The **data** (collected evidence, classifications, article revisions, and timeline outputs) is licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](LICENSE_DATA).
