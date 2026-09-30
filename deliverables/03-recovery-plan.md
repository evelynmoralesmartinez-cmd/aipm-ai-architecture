# 03 · Recovery Plan — Vacation Policy Assistant

**In one line:** stop the wrong answers today, run one cheap test, apply only the fix it points to, and make sure an HR publication can never silently go missing again.

Thresholds and owners are our proposals *(Assumed)* until the product owner and HR confirm them.

---

## Decision

- **Recommendation:** **Contain + Investigate.** Correct only after the test identifies the cause.
- **Decision owner:** product owner of the policy assistant, with HR sign-off.
- **Reason in one sentence:** the old policy clearly reached the model, but we cannot yet tell whether the new one is missing, outranked, hidden or bypassed by a cache, so correcting now would be a guess.
- **What would change it:** many stale policies, or any employee seeing another country's or role's policy → pause the whole assistant, not just vacation answers.

## Actions

| Priority | Horizon | Action | Linked cause or unknown | Impact | Effort | Risk reduction | Confidence | Reversibility | Owner |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Contain | Vacation-entitlement questions → "Please contact HR directly". HR informs employees of the 2026 number | The symptom, whatever the cause | High | Low | Low (hides the symptom) | High | High | HR + AI/IT |
| 2 | Investigate | Keep a copy of the current index, then look up the 2026 policy and replay the question with tracing and no cache | H1–H4 | Medium | Low | Medium | High | High (read-only) | AI/IT |
| 3 | Investigate | Scope check: one question per policy updated since July; search logs for vacation questions since 1 July | Scope *(Unknown)* | Medium | Medium | Medium | High | High | AI/IT + HR |
| 4 | Correct | One fix only, matching the test: re-run ingestion (H1) · retire 2025 + filter by effective date (H2) · fix tags (H3) · clear cache (H4) | Cause confirmed by action 2 | High | Low–Medium | High | Medium until action 2 | High (index copy from action 2) | AI/IT; HR approves tags |
| 5 | Prevent | **Publication check:** every time HR publishes a policy, an automatic question checks that the assistant returns the new value and cites the new version. HR's publishing checklist gets a line: "confirm the old version is retired". The original failure joins the fixed test set | Recurrence | Medium | Medium | High | Medium | High | AI/IT + HR |

**Why this order:** containment works whatever the cause; the test is cheap and read-only, so it goes before the higher-impact correction; and one correction at a time tells us what actually worked.

## Options Considered

| Option | Why it could help | Why not first? | Reversible? |
|---|---|---|---|
| **Non-ML change** — route to HR; publication check; HR checklist | Stops the harm now; closes the gap between "published" and "searchable" | It *is* first, as containment and prevention, but does not repair the index on its own | Yes |
| **Small architecture correction** — re-ingest, retire versions, date filter, tags, cache | Repairs the likely failure point | Cause not verified; the wrong fix wastes effort and can hide the real one | Yes |
| **Larger change** — new model, fine-tuning, new platform | — | No evidence against the model: it used what it received. Fine-tuning does not keep facts current. Costly, and adds governance work | Partly, at high cost |

**Fixes we reject because they do not touch the cause:**
- A prompt instruction like "always use the newest policy" — the model can only choose among passages it receives, and a prompt is not a control.
- Re-uploading every policy before looking — it might hide the symptom, but it destroys the evidence and leaves the cause unknown.

## Proof of Recovery

| Level | Measure or test | Baseline | Pass or guardrail threshold | Owner |
|---|---|---|---|---|
| User or workflow | The original question in 5 wordings | "28 days", cites 2025 | 5/5 answer "29 days from 2026" and cite 2026 | HR |
| User or workflow | Vacation requests HR has to correct because of a wrong entitlement | Not measured | Zero in the pilot week | HR |
| Retrieval or context | Edge cases: employee in another country · different role · "How many days did I have in 2025?" · a question no policy covers | Not measured | Correct version for each; the 2025 question returns 28 *labelled as 2025*; the uncovered question goes to HR | AI/IT |
| Generation or action | Every number in the answer appears in the cited passage, and the citation is the version in force | Wrong citation in the reported case | 100% on the test set; "Please contact HR" counts as a pass when no current policy is found | AI/IT |
| Operations | Time from HR publication to searchable; publication check result | At least 12 weeks in this case | ≤ 1 business day; any failed publication check alerts AI/IT and HR | AI/IT |
| Risk | Employees seeing another country's or role's policy | Not measured | Zero | AI/IT + HR |

## Release and Rollback

- **Release scope:** vacation answers return first for a pilot group — HR staff plus one country — for one week, then everyone.
- **Monitoring:** test set daily during the pilot, weekly afterwards; publication check on every HR publication; weekly review of "Report a wrong answer".
- **Rollback trigger:** any citation of a superseded version, any cross-country or cross-role answer, or any failed test case → back to "Please contact HR" at once.
- **Fallback experience:** "Please contact HR directly", with the official policy link.
- **Remaining risk and owner:** other policies may share the problem until the scope check ends. Accepted by the product owner together with the Head of HR.
