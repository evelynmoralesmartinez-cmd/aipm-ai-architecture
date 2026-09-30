# 02 · Diagnostic Register — Vacation Policy Assistant

**In one line:** the 2025 citation proves the old policy reached the model; we have four explanations for *why*, one test that separates them, and no verified cause.

Status words as in file 01: *Fact (synthetic)* · *Assumed* · *Unknown* · *Requested*. **Verified: none.**

---

## Incident Frame

- **Observed symptom:** the assistant gives 28 vacation days instead of 29 and cites the 2025 policy.
- **Reproducible example:** "How many vacation days do I get per year, and from when?" → "28 days", citing *Vacation Policy 2025*. Expected: "29 days from 2026", citing *Vacation Policy 2026*. *(Fact, synthetic)*
- **Affected users or workflow:** employees checking their entitlement before a request. Anyone beyond the reporting employee: *Unknown*.
- **Impact and severity:** medium, by our judgement. The employee plans with one day less and trust drops; harm is limited because the answer only informs and HR reviews every request.
- **First observed:** 22 September 2026 *(Fact, synthetic)*.
- **Recent relevant changes:** HR published the 2026 policy (28 → 29 days) on 1 July 2026 *(Fact, synthetic)*.
- **Immediate containment:** vacation-entitlement questions get "Please contact HR directly"; HR tells employees the correct 2026 number.
- **Facts frozen for this case:** the request and answer, the expected answer, the two dates, the policy change. Nothing else.

## Evidence Available

We only list what the case actually gives us. No logs were invented.

| Evidence | Source | What it shows | Limitation | Status |
|---|---|---|---|---|
| Employee report with question and answer | Employee → HR | The assistant said 28 days | One case; scope unknown | Fact (synthetic) |
| Citation in the answer: *Vacation Policy 2025* | Assistant output | The 2025 version reached the model and was used | Says nothing about whether 2026 is in the index | Fact (synthetic) |
| 2026 policy in the HR repository since 1 July | HR repository | The correct source exists | Existing in the repository ≠ reaching the index | Fact (synthetic) |
| 2025 version still searchable | Deduced from the citation | Old versions are not retired | Assumes citations reflect retrieved passages | Inferred |

### Evidence we would request

| Request | What it could reveal | From |
|---|---|---|
| Index search for the 2026 policy, with its tags | Missing, present, or present with wrong tags | AI/IT |
| Ingestion job record around 1 July | Whether the 2026 load ran and succeeded | AI/IT |
| Traced replay of the failing question, cache bypassed | Ranked passages, their dates, and what reached the model | AI/IT |
| Cache settings and age of the served answer | Whether retrieval was skipped | AI/IT |
| Other policies HR updated since July | Whether this is one document or a systemic gap | HR + AI/IT |

## Competing Hypotheses

**How we ranked them:** the 2025 citation rules the model out as the first suspect, so we ordered causes by how directly they explain an old version reaching the model, and pushed down those that need extra conditions.

| Rank | Hypothesis and component | Why it fits | Evidence against it | Next discriminating test | Confidence |
|---|---|---|---|---|---|
| 1 | **Missing ingestion** — the 2026 policy never reached the index *(ingestion pipeline)* | If only 2025 is searchable, the assistant can only cite 2025 | None yet. If ingestion failed, other July updates may be stale too (*Unknown*) | Search the index for the 2026 policy; read the ingestion job record | Medium |
| 2 | **Version conflict** — both versions are indexed and nothing tells retrieval which one is in force *(index metadata + retrieval)* | The two documents are almost identical, so the older one can rank first | None yet; cannot be separated from H1 without looking at the index | If 2026 is indexed: replay and inspect ranked passages and their effective dates | Medium |
| 3 | **Hidden by tags** — the 2026 policy is tagged for the wrong country or role, so the permission filter hides it from this employee *(metadata + role-based retrieval)* | Retrieval only shows the employee's permitted set; 2025 would be the only version they can see | Less likely if employees across roles and countries are affected | Replay as employees with different roles and countries; read the 2026 tags | Low |
| 4 | **Stale cache** — an answer stored before July is served again *(serving layer)* | A pre-July answer would say 28 days | Almost 12 weeks since publication; most caches expire sooner (*Inferred*) | Replay with cache bypassed; check the age of the served answer | Low |

**Not listed on purpose:** a model or generation failure. The model cited the version it received; nothing points at its behaviour. The replay in H2 still shows the final context, so it would surface this at no extra cost.

## Next-Best Evidence

- **Request:** look up the 2026 policy in the index with its tags, then replay the failing question with tracing and the cache bypassed.
- **Why it separates the hypotheses** — one test, four readings:

| What we see | Points to | Fix it would unlock |
|---|---|---|
| 2026 is not in the index | H1 | Re-run ingestion; alert on failed loads |
| 2026 is there with correct tags, 2025 ranks higher | H2 | Retire 2025; filter by effective date |
| 2026 is there with the wrong country or role | H3 | Correct the tags with HR |
| The replay correctly says 29 days | H4 (or already fixed) | Clear the cache; shorten its lifetime |

- **Who:** AI/IT platform team runs it; HR confirms what the correct tags are.
- **Decision that depends on it:** which single correction to apply.

## AI Challenge Note

After writing our own map and hypotheses, we asked Claude to act as the handout's skeptical review panel, using only this synthetic case. Decisions below are ours; none of the suggestions is evidence.

| LLM suggestion | How we checked it | Decision | Reason |
|---|---|---|---|
| A cause was missing: the 2026 policy could be in the index but hidden by wrong country or role tags | Against the map: role-based retrieval is a known component and tagging happens at ingestion | **Accepted** → H3 | Plausible, distinct from H1 and H2, and tested by the same index lookup |
| Our first containment idea, "ask for the policy to be re-uploaded", assumes the cause | Against the evidence: nobody has looked at the index yet | **Accepted** → moved to Correct, after investigation | Re-uploading before inspecting would also erase the evidence for H1 and H2 |
| The model might have ignored a retrieved 2026 passage | Against the evidence: the answer cites 2025, not 2026 | **Edited** → checked inside the H2 replay, not a separate hypothesis | Unlikely given the citation, but free to confirm |

## Current Diagnosis

- **Verified cause:** none.
- **Best-supported hypothesis:** an index-level problem — H1 or H2. The citation shows the old version was available; we cannot yet say whether the new one is missing or losing.
- **Important uncertainty:** how many employees and how many other policies are affected.
- **What would change the diagnosis:** the index lookup and replay; or finding other July updates stale, which would point firmly at H1.
