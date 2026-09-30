# 05 · Reflection

**Which evidence changed our ranking of root causes most?**
The citation. Because the answer named the 2025 policy, we knew the old version had reached the model and that the model had used what it was given. That moved the model from first suspect to last and pointed us at the index. The dates did the rest: nearly twelve weeks between publication and the report made a cached answer unlikely.

**Which attractive fix did we reject because it was unsupported or too broad?**
Two. Changing or fine-tuning the model, because nothing points at it and fine-tuning cannot keep a policy current. And our own first idea — "just re-upload the policy" — because it assumed the cause, and doing it before inspecting the index would have erased the evidence we needed.

**Where does accountability sit when the model, data and workflow have different owners?**
HR owns what the policy says and when it changes. The AI/IT team owns whether the assistant can find it. The product owner of the assistant makes the call and accepts the remaining risk, with HR signing off. The gap in this case sits exactly between the first two: "published" and "searchable" were never checked as one step, which is why the publication check belongs to both teams.

**Which part of the architecture would be hardest to observe during a real incident?**
Ingestion. It runs when HR publishes, not when employees ask, so a silent failure leaves no trace in the answers until someone notices a wrong number. We could only reason about it from the outside, through the citation.
