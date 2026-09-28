<!-- SPDX-License-Identifier: CC-BY-4.0 · Copyright (c) 2026 Rulehold -->
# Model lineage blind spots on Hugging Face — v1 (aggregates only)

- Snapshot `20260927T072129324Z` (2026-09-27 07:21–07:28 UTC), Hugging Face public Hub API, unauthenticated.
- **Aggregates only.** No per-model verdicts and no model ids are published.
- Lineage is *inferred* from public metadata (self-declared `base_model`, model architecture, weight-file hashes, repository name). It is an estimate, not a compliance guarantee.

## Metrics
- **Blind-spot rate S**: among models whose lineage could be inferred, the share whose repository name lacks the family marker (M1) or whose self-declared `base_model` is missing or inconsistent with the inferred lineage (M2), union.
- **M3 (undetermined)**: share of fetched models whose lineage could not be determined — these are blocked under a fail-closed policy.
- **M4**: share of Korean samples inferred to have Chinese-origin lineage. **Lower bound**: undetermined models and architectures outside the rule set are not counted.
- **Population S range**: counting undetermined models as none-to-all blind spots (denominator = fetched models).
- Families covered: deepseek, qwen, llama, mistral, gemma, exaone, glm, minimax, kimi, nemotron, gpt-oss, phi, smollm, lfm, granite, olmo. Anything else counts as undetermined.
- Intervals are 95% Wilson score intervals.

| Sample | Fetched | Inferred | S (among inferred) | Population S range | M1 | M2 | M3 | M4 (lower bound) |
|---|---|---|---|---|---|---|---|---|
| C popular text-generation models (unfiltered) | 200 | 154 | 47.4% (73/154, 95% 39.7–55.3%) | 36.5–59.5% (inferred 77.0%) | 27.9% (43/154, 95% 21.4–35.5%) | 36.4% (56/154, 95% 29.2–44.2%) | 23.0% (46/200, 95% 17.7–29.3%) | — |
| B1 Korean-named models | 100 | 70 | 34.3% (24/70, 95% 24.2–46.0%) | 24.0–54.0% (inferred 70.0%) | 32.9% (23/70, 95% 23.0–44.5%) | 8.6% (6/70, 95% 4.0–17.5%) | 30.0% (30/100, 95% 21.9–39.6%) | 18.0% (18/100, 95% 11.7–26.7%) |
| B2 Korean-tagged models (auxiliary) | 100 | 64 | 31.3% (20/64, 95% 21.2–43.4%) | 20.0–56.0% (inferred 64.0%) | 14.1% (9/64, 95% 7.6–24.6%) | 26.6% (17/64, 95% 17.3–38.5%) | 36.0% (36/100, 95% 27.3–45.8%) | 15.0% (15/100, 95% 9.3–23.3%) |
| A declared derivatives of base models | 200 | 199 | — | — | 12.1% (24/199, 95% 8.2–17.3%) | — | 0.5% (1/200, 95% 0.1–2.8%) | — |

- Sample A is drawn from the self-declared derivative tree, so M2 and S are not computed for it.
- 17 models appear in more than one sample; samples are reported separately and not pooled.

## How to read it
- At least about a third of popular text-generation models (C, lower bound 36.5%) cannot be traced to their lineage from the name and the self-declaration alone; structure or weight-hash evidence is needed.
- 30 of the 154 inferred models in C are inferred from architecture only. By construction they are all blind spots (no name marker and no declaration). If they are counted as undetermined instead — the stricter view a fail-closed gateway would take — C's population range is **21.5–59.5%** (62.0% inferred). Both readings are shown because the range width depends on this choice.
- Architecture only means "same architecture", not "derived from"; a model trained from scratch on a shared architecture can be counted here, which biases S upward. Architecture-only inference is never used to *allow* a model in our rule set.
- M4 for Korean-named models (18.0%) is a lower bound.

## Limitations
- One point in time (2026-09-27); download rankings change daily. Popularity bias in C; B1 only catches models with "korean" in the name; B2 (language tag) mixes in multilingual models.
- Quantized files without weight hashes (e.g. GGUF) and architectures missing from the rule set fall into M3. When M3 is large, part of the blind spot sits in M3 rather than S.
- GGUF architecture names are coarse, so they are used only to confirm other evidence or for architecture-only inference, never to contradict it.
- The family/architecture table must be kept current; new architectures become undetermined until added.

## Reproduce
1. Samples (Hugging Face `GET /api/models`, sorted by downloads, descending):
   - C: `pipeline_tag=text-generation&sort=downloads&direction=-1&limit=400`, first 200 after exclusions
   - B1: `search=korean&pipeline_tag=text-generation&sort=downloads&direction=-1&limit=150`, first 100
   - B2: `filter=ko&pipeline_tag=text-generation&sort=downloads&direction=-1&limit=150`, first 100
   - A: declared derivatives (`base_model` tree) of 10 widely used base models from 6 publisher organisations, 20 per base model. The base-model list is not published in this version (no model ids).
   - Exclusions: repositories of the official family publishers and obvious test repositories.
2. For each model: `GET /api/models/<id>?blobs=true` (config architecture, `base_model`, safetensors hashes, GGUF metadata) plus one level of declared parents. Sequential, ≥1.1 s between requests, stop after 3 consecutive HTTP 429, cache only HTTP 200. This snapshot took 398 requests.
3. Infer lineage per model from: official weight-file hashes, architecture (strong/weak), declared `base_model`, and family-name tokens in the repository name. Name and declaration are then checked against the inferred family to get M1/M2.
4. Aggregate per sample with 95% Wilson intervals → `data/summary.json`. The lineage engine itself is not part of this repository.

## Files
| File | Content |
|---|---|
| `data/summary.json` | All aggregates above plus GGUF share, S without GGUF, inferred-family counts per sample, sample overlap |
| `data/breakdown.json` | Breakdown tables by family and GGUF (v0.2.0) |
| `*.sha256` | SHA-256 of each JSON |

## Breakdown by family and GGUF (v0.2.0, snapshot `20260927T072129324Z`)

- A cell is published only if it has at least 5 inferred models and its blind spots are neither none nor all of them (in the GGUF table, at least one fetched model must also be undetermined). Only cells with fewer than 5 inferred models are merged into "other"; if any larger cell or the merged "other" fails the rule, the whole table is left out. No value is hidden, so every table adds up to the sample total. Family cells count inferred models only, so M3 is not shown there.

#### C — GGUF vs non-GGUF repositories

| Cell | Fetched | Inferred | S (among inferred) | M3 |
|---|---|---|---|---|
| non-gguf | 134 | 93 | 54.8% (51/93, 95% 44.7–64.6%) | 30.6% (41/134, 95% 23.4–38.8%) |
| gguf | 66 | 61 | 36.1% (22/61, 95% 25.2–48.6%) | 7.6% (5/66, 95% 3.3–16.5%) |

- C: the table by inferred family is not published (its cells could not meet the rule).

#### B1 — by inferred family

| Cell | Fetched | Inferred | S (among inferred) | M3 |
|---|---|---|---|---|
| llama | — | 36 | 13.9% (5/36, 95% 6.1–28.7%) | — |
| qwen | — | 18 | 44.4% (8/18, 95% 24.6–66.3%) | — |
| mistral | — | 11 | 72.7% (8/11, 95% 43.4–90.3%) | — |
| other (gemma, lfm) | — | 5 | 60.0% (3/5, 95% 23.1–88.2%) | — |

#### B1 — GGUF vs non-GGUF repositories

| Cell | Fetched | Inferred | S (among inferred) | M3 |
|---|---|---|---|---|
| gguf | 67 | 54 | 31.5% (17/54, 95% 20.7–44.7%) | 19.4% (13/67, 95% 11.7–30.4%) |
| non-gguf | 33 | 16 | 43.8% (7/16, 95% 23.1–66.8%) | 51.5% (17/33, 95% 35.2–67.5%) |

#### B2 — by inferred family

| Cell | Fetched | Inferred | S (among inferred) | M3 |
|---|---|---|---|---|
| lfm | — | 38 | 21.1% (8/38, 95% 11.1–36.3%) | — |
| qwen | — | 14 | 64.3% (9/14, 95% 38.8–83.7%) | — |
| other (exaone, gemma, glm, granite, mistral, nemotron, phi) | — | 12 | 25.0% (3/12, 95% 8.9–53.2%) | — |

#### B2 — GGUF vs non-GGUF repositories

| Cell | Fetched | Inferred | S (among inferred) | M3 |
|---|---|---|---|---|
| non-gguf | 70 | 36 | 36.1% (13/36, 95% 22.5–52.4%) | 48.6% (34/70, 95% 37.2–60.0%) |
| gguf | 30 | 28 | 25.0% (7/28, 95% 12.7–43.4%) | 6.7% (2/30, 95% 1.8–21.3%) |

- Why the C family table is missing: at least one family cell with 5 or more inferred models failed the rule, so the whole table is left out (which cell is not disclosed).
- `data/breakdown.json` holds the same tables. The GGUF table's S is also derivable from `S` and `S_noGguf` in `data/summary.json`; its new information is M3 per cell.
- Future snapshots are compared with every earlier release: a sample whose membership or public metadata changed by 1–4 models is withheld entirely, and family tables and per-family counts are left out whenever anything changed.

## 한국어 요약

# Hugging Face 모델 계보 사각지대 실측 v1 (집계만)

- 스냅숏 `20260927T072129324Z`(2026-09-27 07:21~07:28 UTC), Hugging Face 공개 Hub API, 비인증.
- **집계만** 싣는다. 개별 모델의 판정과 모델 id는 싣지 않는다.
- 판정은 공개 메타데이터(자기신고 `base_model`, 모델 구조, 가중치 파일 해시, 저장소 이름)로 추정한 값이며 규정 준수를 보증하지 않는다.

## 지표
- **사각지대율 S**: 계보가 판정된 모델 가운데, 저장소 이름에 계열 표시가 없거나(M1) `base_model` 자기신고가 없거나 판정과 다른(M2) 모델의 비율(합집합).
- **M3 확인 불가율**: 계보를 판정하지 못한 모델의 비율(기본 차단 정책이면 차단되는 비율).
- **M4**: 한국어 표본 가운데 중국계 계보로 판정된 비율(**하한**: 판정하지 못한 모델과 판정 규칙 밖 중국계 구조는 세지 않는다).
- **모집단 S 범위**: 판정하지 못한 모델을 사각지대 0개~전부로 둘 때의 범위(분모 = 조회 성공).
- 판정 계열: deepseek, qwen, llama, mistral, gemma, exaone, glm, minimax, kimi, nemotron, gpt-oss, phi, smollm, lfm, granite, olmo. 이 목록 밖 계열은 "확인 불가"로 센다.
- 구간은 95% Wilson 구간이다.

| 표본 | 조회 성공 | 판정 | S(판정된 모델 안) | 모집단 S 범위 | M1 | M2 | M3 | M4(하한) |
|---|---|---|---|---|---|---|---|---|
| C 필터 없는 인기 모델 | 200 | 154 | 47.4% (73/154, 95% 39.7–55.3%) | 36.5–59.5% (판정 77.0%) | 27.9% (43/154, 95% 21.4–35.5%) | 36.4% (56/154, 95% 29.2–44.2%) | 23.0% (46/200, 95% 17.7–29.3%) | — |
| B1 이름에 korean | 100 | 70 | 34.3% (24/70, 95% 24.2–46.0%) | 24.0–54.0% (판정 70.0%) | 32.9% (23/70, 95% 23.0–44.5%) | 8.6% (6/70, 95% 4.0–17.5%) | 30.0% (30/100, 95% 21.9–39.6%) | 18.0% (18/100, 95% 11.7–26.7%) |
| B2 한국어 태그(보조) | 100 | 64 | 31.3% (20/64, 95% 21.2–43.4%) | 20.0–56.0% (판정 64.0%) | 14.1% (9/64, 95% 7.6–24.6%) | 26.6% (17/64, 95% 17.3–38.5%) | 36.0% (36/100, 95% 27.3–45.8%) | 15.0% (15/100, 95% 9.3–23.3%) |
| A 기반 모델 파생(자기신고 트리) | 200 | 199 | — | — | 12.1% (24/199, 95% 8.2–17.3%) | — | 0.5% (1/200, 95% 0.1–2.8%) | — |

- 표본 A는 자기신고 트리에서 뽑았으므로 M2와 S를 계산하지 않는다.
- 여러 표본에 동시에 든 모델은 17개다. 표본별 값은 합치지 않았다.

## 읽는 법
- 인기 텍스트 생성 모델(C)의 적어도 약 3분의 1(하한 36.5%)은 이름과 자기신고만으로는 계보를 알 수 없고, 구조·가중치 해시 근거가 있어야 보인다.
- C에서 판정된 154개 중 30개는 구조로만 추정했다. 정의상 모두 사각지대다(이름 표시·신고 없음). 이 30개를 확인 불가로 세면(기본 차단 게이트웨이와 같은 엄격한 기준) C의 모집단 범위는 **21.5–59.5%**(판정 62.0%)다. 폭이 이 선택에 따라 달라지므로 두 읽기를 함께 적는다.
- 구조로만 추정은 "같은 구조"라는 뜻이지 "파생"이 아니다. 같은 구조로 처음부터 학습한 모델이 섞일 수 있어 S 쪽으로 치우친다. 우리 판정 규칙에서 구조로만 추정은 모델을 **허용하는 근거로 쓰지 않는다**.
- 한국어 이름 모델의 M4(18.0%)는 하한이다.

## 한계
- 시점 1회(2026-09-27)다. 다운로드 순위는 날마다 바뀐다. C는 인기 편향, B1은 이름에 "korean"을 넣은 모델만, B2(언어 태그)는 다국어 모델이 섞인다.
- 가중치 해시가 없는 양자화 파일(GGUF 등)과 판정 규칙에 없는 구조는 M3로 빠진다. M3가 크면 사각지대 일부가 S가 아니라 M3에 있다.
- GGUF 구조 이름은 거칠어서 다른 근거를 확인하거나 구조로만 추정할 때만 쓰고, 모순 판단에는 쓰지 않는다.
- 계열·구조표를 계속 갱신해야 한다. 새 구조는 표에 들어가기 전까지 확인 불가다.

## 재현
1. 표본(Hugging Face `GET /api/models`, 다운로드 내림차순)
   - C: `pipeline_tag=text-generation&sort=downloads&direction=-1&limit=400`, 제외 뒤 상위 200
   - B1: `search=korean&pipeline_tag=text-generation&sort=downloads&direction=-1&limit=150`, 상위 100
   - B2: `filter=ko&pipeline_tag=text-generation&sort=downloads&direction=-1&limit=150`, 상위 100
   - A: 널리 쓰이는 기반 모델 10개(발행 조직 6곳)의 자기신고 파생(`base_model` 트리), 기반 모델마다 20개. 이번 판에는 기반 모델 목록을 싣지 않는다(모델 id 0).
   - 제외: 계열 공식 발행 조직의 저장소와 명백한 시험용 저장소.
2. 모델마다 `GET /api/models/<id>?blobs=true`(config 구조, `base_model`, safetensors 해시, GGUF 메타데이터)와 신고된 부모 한 단계. 순차, 요청 간격 1.1초 이상, HTTP 429가 3회 연속이면 중단, HTTP 200만 캐시. 이 스냅숏은 요청 398회.
3. 공식 가중치 해시, 구조(강·약), 신고된 `base_model`, 저장소 이름의 계열 이름 토큰으로 모델별 계보를 추정하고, 이름·신고를 추정 계열과 대조해 M1·M2를 낸다.
4. 표본별로 95% Wilson 구간과 함께 집계 → `data/summary.json`. 판정 엔진 자체는 이 저장소에 없다.

## 계열·GGUF별 나눠 보기 (v0.2.0, 스냅숏 `20260927T072129324Z`)

- 판정된 모델이 5개 이상이고 사각지대가 하나도 없거나 전부인 경우가 아닌 칸만 싣는다(GGUF 표는 확인 불가도 1개 이상이어야 한다). 판정 5개 미만 칸만 "기타"로 합치고, 더 큰 칸이나 합친 기타가 규칙을 못 맞추면 그 표 전체를 싣지 않는다. 가린 값이 없으므로 표마다 합계가 표본 합계와 같다. 계열 칸은 판정된 모델만 세므로 M3를 싣지 않는다.

#### C — GGUF 저장소 여부별

| 칸 | 조회 성공 | 판정 | S(판정된 모델 안) | M3 |
|---|---|---|---|---|
| non-gguf | 134 | 93 | 54.8% (51/93, 95% 44.7–64.6%) | 30.6% (41/134, 95% 23.4–38.8%) |
| gguf | 66 | 61 | 36.1% (22/61, 95% 25.2–48.6%) | 7.6% (5/66, 95% 3.3–16.5%) |

- C: 추정 계열별 표는 싣지 않는다(칸이 규칙을 만족하지 못함).

#### B1 — 추정 계열별

| 칸 | 조회 성공 | 판정 | S(판정된 모델 안) | M3 |
|---|---|---|---|---|
| llama | — | 36 | 13.9% (5/36, 95% 6.1–28.7%) | — |
| qwen | — | 18 | 44.4% (8/18, 95% 24.6–66.3%) | — |
| mistral | — | 11 | 72.7% (8/11, 95% 43.4–90.3%) | — |
| 기타 (gemma, lfm) | — | 5 | 60.0% (3/5, 95% 23.1–88.2%) | — |

#### B1 — GGUF 저장소 여부별

| 칸 | 조회 성공 | 판정 | S(판정된 모델 안) | M3 |
|---|---|---|---|---|
| gguf | 67 | 54 | 31.5% (17/54, 95% 20.7–44.7%) | 19.4% (13/67, 95% 11.7–30.4%) |
| non-gguf | 33 | 16 | 43.8% (7/16, 95% 23.1–66.8%) | 51.5% (17/33, 95% 35.2–67.5%) |

#### B2 — 추정 계열별

| 칸 | 조회 성공 | 판정 | S(판정된 모델 안) | M3 |
|---|---|---|---|---|
| lfm | — | 38 | 21.1% (8/38, 95% 11.1–36.3%) | — |
| qwen | — | 14 | 64.3% (9/14, 95% 38.8–83.7%) | — |
| 기타 (exaone, gemma, glm, granite, mistral, nemotron, phi) | — | 12 | 25.0% (3/12, 95% 8.9–53.2%) | — |

#### B2 — GGUF 저장소 여부별

| 칸 | 조회 성공 | 판정 | S(판정된 모델 안) | M3 |
|---|---|---|---|---|
| non-gguf | 70 | 36 | 36.1% (13/36, 95% 22.5–52.4%) | 48.6% (34/70, 95% 37.2–60.0%) |
| gguf | 30 | 28 | 25.0% (7/28, 95% 12.7–43.4%) | 6.7% (2/30, 95% 1.8–21.3%) |

- C 계열 표가 없는 이유: 판정 5개 이상인 계열 칸 가운데 적어도 하나가 규칙을 못 맞춰 표 전체를 뺐다(어느 칸인지는 밝히지 않는다).
- 같은 표가 `data/breakdown.json`에 있다. GGUF 표의 S는 `data/summary.json`의 `S`·`S_noGguf`로도 계산되며, 새 정보는 칸별 M3다.
- 다음 스냅숏부터는 이전 모든 판과 비교한다. 구성이나 공개 메타데이터가 1~4개 바뀐 표본은 통째로 싣지 않고, 바뀜이 있으면 계열 표와 계열별 수를 싣지 않는다.
