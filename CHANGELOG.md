# Changelog

## v0.1.1 — 2026-09-28
- Headline wording: the 18.0% Chinese-origin figure is an *inferred* lineage and a lower bound (README, dataset card).
- Issue forms for sample requests, questions and data corrections (labels `topic:sample`, `topic:question`, `topic:correction`); `CITATION.cff`.
- Release notes now contain only the tagged version's section; `huggingface_hub` pinned to an exact version.
- Data unchanged: `data/summary.json` is byte-identical to v0.1.0.

## v0.1.0 — 2026-09-28
- First public release: aggregate lineage blind-spot measurements (rule set v1) on the 2026-09-27 Hugging Face snapshot.
- Files: `blindspot-public.md` (English + Korean), `data/summary.json`, `data/summary.json.sha256`.
- Aggregates only; no model ids or per-model verdicts.
