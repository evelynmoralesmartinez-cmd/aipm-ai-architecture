# 04 · Decision Slides — Vacation Policy Assistant

The presented deck has three slides (yellow, orange, red). Deck: **[paste the shared link or add the exported PDF here]**

This file gives the text of each slide, so it can be read without the deck. All case details are synthetic.

---

## Slide 1 — What is broken

**Headline:** Outdated vacation answers from the policy assistant

**Flow shown:** HR publishes policy → split and tag versions → **policy index (suspect)** → role-based retrieval → LLM and citation check.
Likely failure zone *(inferred)*: the index, where ingestion hands content to serving.

| Symptom · *fact, synthetic* | Impact · *fact + unknown* | Containment · *done today* |
|---|---|---|
| Asked for yearly vacation days, the assistant says "28 days" and cites the 2025 policy. Expected: "29 days from 2026" | One day less planned per employee. How many employees: unknown. Severity medium — HR approves every request | Vacation questions go to HR; HR tells employees the correct 2026 number |

*Footer:* 2026 policy published 1 Jul 2026 · error reported 22 Sep 2026

## Slide 2 — Why we think it is broken

**Headline:** The old policy reached the model; the index is the suspect

| # | Hypothesis | Next test | Confidence |
|---|---|---|---|
| 1 | Missing ingestion: 2026 never reached the index | Search the index; check the ingestion job | Medium |
| 2 | Version conflict: both indexed, no effective-date filter | Replay; inspect ranked passages and dates | Medium |
| 3 | Wrong tags: 2026 marked for another country or role | Replay as other roles; read the tags | Low |
| 4 | Stale cache: an old answer served again | Replay without cache; check its age | Low |

- **Observed:** the answer cites the 2025 policy — the model used what it received.
- **Missing:** retrieval logs, index contents, number of affected employees.
- **Next-best test:** look up the 2026 policy and its tags, then replay without cache. One test, four outcomes.

*Footer:* Rejected: switching or fine-tuning the model — no evidence points to it.

## Slide 3 — What we recommend

**Headline:** Contain and investigate now, then correct

| Step | Action | Owner |
|---|---|---|
| 1 Contain | Route vacation questions to HR; inform affected employees | HR + AI/IT |
| 2 Investigate | Index lookup and replay; check other policies updated since July | AI/IT |
| 3 Correct | Only the fix the test points to | AI/IT, HR approves tags |
| 4 Prevent | Publication check on every HR release; original failure becomes a permanent test | AI/IT + HR |

- **Proof of recovery:** 5 wordings of the original question → "29 days", 2026 citation, 5/5; publication searchable within 1 day.
- **Release and rollback:** one-week pilot; any outdated citation or cross-role answer → back to HR routing.
- **What would change this:** many stale policies or cross-role leaks → pause the whole assistant.

*Footer:* Decision owner: product owner of the assistant, with HR sign-off.
