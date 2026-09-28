---
license: cc-by-4.0
language:
- en
- ko
pretty_name: Rulehold lineage-blindspot v0
tags:
- model-lineage
- huggingface-hub
- aggregates
size_categories:
- n<1K
viewer: false
---
<!-- SPDX-License-Identifier: CC-BY-4.0 · Copyright (c) 2026 Rulehold -->
# Rulehold lineage-blindspot v0

Aggregate measurements of model-lineage blind spots on Hugging Face: how often a text-generation model's base-model family cannot be seen from its repository name or self-declared `base_model`, and how often it cannot be determined at all. Snapshot 2026-09-27, public Hub API.

- `summary.json` — aggregates per sample (counts, rates, 95% Wilson intervals, inferred-family counts); `summary.json.sha256` — checksum.
- `blindspot-public.md` — definitions, tables, limitations and reproduction procedure (English, then Korean).
- **Aggregates only**: no model ids and no per-model verdicts.
- Headline: among the 200 most-downloaded text-generation models, 36.5–59.5% are lineage blind spots at population level (21.5–59.5% under a stricter reading); among 100 Korean-named models at least 18.0% have Chinese-origin lineage (lower bound).
- Lineage is inferred from public metadata and is an estimate, not a compliance guarantee.

Source & releases: GitHub `rulehold/lineage-blindspot`. License: CC BY 4.0 — cite "Rulehold, lineage-blindspot v0.1.0".

## 한국어
Hugging Face 텍스트 생성 모델의 기반 모델 계열이 저장소 이름·자기신고 `base_model`로 보이지 않는 비율과 판정 자체가 안 되는 비율을 잰 집계입니다(2026-09-27 스냅숏). 모델 id와 모델별 판정은 싣지 않습니다. 정의·표·한계·재현은 `blindspot-public.md`에 있습니다. 라이선스 CC BY 4.0.

Contact: hello@rulehold.com · Updates: https://rulehold.com/?utm_source=hf-dataset&utm_medium=tool&utm_campaign=lineage-blindspot-v0.1.0
