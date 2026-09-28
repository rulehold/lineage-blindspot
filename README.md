# Rulehold lineage-blindspot

How often can you *not* tell where an open-weight model comes from? Aggregate measurements of model-lineage blind spots on Hugging Face.

- **What it measures**: for samples of Hugging Face text-generation models, the share whose lineage (base-model family) cannot be seen from the repository name or the self-declared `base_model`, and the share whose lineage cannot be determined at all.
- **Headline (2026-09-27 snapshot)**: among the 200 most-downloaded text-generation models, **36.5–59.5%** are lineage blind spots at population level (21.5–59.5% under a stricter reading); 23.0% could not be determined at all. Among 100 Korean-named models, at least **18.0%** have an *inferred* Chinese-origin lineage (lower bound; inferred from public metadata, not confirmed).
- **Aggregates only**: no model ids and no per-model verdicts are published.

Full tables, definitions, limitations and the reproduction procedure: [`blindspot-public.md`](blindspot-public.md).

## Files
| File | Content |
|---|---|
| `blindspot-public.md` | Report (English, then Korean) |
| `data/summary.json` | Aggregates per sample (counts, rates, 95% Wilson intervals, family counts) |
| `data/summary.json.sha256` | Checksum |
| `data/breakdown.json` (+ `.sha256`) | v0.2.0: S and M3 broken down by inferred family and by GGUF vs non-GGUF, under a small-cell rule that never hides single values |

Release assets for each tag carry the report and the data files. The dataset is mirrored on Hugging Face: `rulehold/lineage-blindspot-v0`.

### `data/summary.json` fields (per sample)
| Field | Meaning |
|---|---|
| `models`, `fetchedOk`, `judged`, `unknown` | sample size, fetched, lineage inferred, undetermined |
| `S`, `M1`, `M2`, `M3`, `M4` | `{k, n, rate, low, high}` — count, denominator, rate and 95% Wilson interval |
| `populationS` | `{min, max, judgedShare}` — undetermined counted as none-to-all blind spots |
| `ggufShare`, `S_noGguf` | GGUF share; S excluding GGUF repositories |
| `families` | inferred-family counts |
| `familiesCovered` (top level) | families the rule set can infer; others count as undetermined |
| `withheld` (top level) | samples left out of this release under the snapshot-difference rule (empty in v0.2.0) |

## Method & limits
Lineage is inferred from public metadata (weight-file hashes, architecture, declared `base_model`, name tokens). It is an estimate, not a compliance guarantee. One snapshot, popularity-biased samples, M4 is a lower bound. Details in the report.

## License
Data and report: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — please cite "Rulehold, lineage-blindspot v0.2.0" (see [`CITATION.cff`](CITATION.cff)). Workflow code: MIT. See [`LICENSE`](LICENSE). Source metadata: Hugging Face public Hub API.

## Questions & requests
Open an issue with one of the forms: sample request, question, or data correction. We do not publish or send verdicts for individual models.

## Updates
Next snapshot is planned; changes are listed in [`CHANGELOG.md`](CHANGELOG.md).
Contact: hello@rulehold.com · Updates: https://rulehold.com/?utm_source=gh-release&utm_medium=tool&utm_campaign=lineage-blindspot-v0.2.0

## 한국어

오픈 웨이트 모델의 출신(기반 모델 계열)을 이름·자기신고만으로는 알 수 없는 경우가 얼마나 되는지, Hugging Face에서 잰 집계입니다.

- **무엇을 재나**: Hugging Face 텍스트 생성 모델 표본에서, 저장소 이름이나 자기신고 `base_model`로는 계보가 보이지 않는 비율과 계보를 아예 판정할 수 없는 비율.
- **요약(2026-09-27 스냅숏)**: 다운로드 상위 텍스트 생성 모델 200개 가운데 모집단 기준 **36.5–59.5%**가 계보 사각지대입니다(엄격한 읽기 21.5–59.5%). 23.0%는 판정 자체가 안 됩니다. 이름에 korean이 든 모델 100개 가운데 적어도 **18.0%**는 중국계 계보로 *추정*됩니다(하한, 공개 메타데이터 기반 추정이며 확정 판정이 아님).
- **집계만** 싣습니다. 모델 id와 모델별 판정은 싣지 않습니다.

표·정의·한계·재현 절차 전체: [`blindspot-public.md`](blindspot-public.md)(영어 뒤 한국어).

- 파일: `blindspot-public.md`(보고서), `data/summary.json`(표본별 집계, 95% Wilson 구간), `data/breakdown.json`(v0.2.0: 계열·GGUF별 나눠 보기, 작은 칸 규칙), 각 `.sha256`(체크섬). 태그마다 릴리스 자산으로 같은 파일을 올립니다. Hugging Face 데이터셋 `rulehold/lineage-blindspot-v0`에도 같은 파일이 있습니다.
- 방법과 한계: 공개 메타데이터(가중치 해시, 구조, `base_model` 신고, 이름 토큰)로 추정한 값이며 규정 준수를 보증하지 않습니다. 시점 1회, 인기 편향 표본, M4는 하한입니다.
- 라이선스: 데이터·보고서 CC BY 4.0("Rulehold, lineage-blindspot v0.2.0"으로 출처 표시, `CITATION.cff` 참고), 워크플로 코드 MIT. 원천 메타데이터: Hugging Face 공개 Hub API.
- 문의·요청: GitHub 이슈 양식(표본 요청·질문·데이터 정정). 개별 모델 판정은 공개하거나 보내지 않습니다.
- 연락: hello@rulehold.com · 소식 받기: https://rulehold.com/?utm_source=gh-release&utm_medium=tool&utm_campaign=lineage-blindspot-v0.2.0
