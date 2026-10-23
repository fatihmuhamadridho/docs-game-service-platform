# [Feature Name] - Task Detail Questions

> **Document Type:** Open Questions  
> **Status:** Draft  
> **Story / AC Link:** [Link or document path]  
> **Frontend Task Link:** [Link or document path / N/A]  
> **Backend Task Link:** [Link or document path / N/A]  
> **Owner:** [Person or team]

Record decisions needed to finalize frontend or backend task breakdowns. Ask about scope, ownership, dependencies, estimates, or delivery boundaries. Use `TBD` for unknown owners or dates.

## Questions

| ID         | Area             | Related Task / AC                | Question                                                     | Impact If Unresolved                                    | Asked To      | Needed By     | Status |
| ---------- | ---------------- | -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------- | ------------- | ------------- | ------ |
| TASK-Q-001 | [FE / BE / Both] | [FE-001 / BE-001 / AC-001 / TBD] | [Which task detail or delivery decision needs confirmation?] | [How scope, estimate, owner, or dependency is affected] | [Role or TBD] | [Date or TBD] | Open   |

## Decisions

Add one row when an answer is confirmed. Record source and update affected task details; do not treat an assumption as a decision.

| Question ID | Confirmed Answer / Decision | Source                              | Decided On    | Task Detail Update                                |
| ----------- | --------------------------- | ----------------------------------- | ------------- | ------------------------------------------------- |
| TASK-Q-001  | [Decision or TBD]           | [Meeting, ticket, or document link] | [Date or TBD] | [Task ID, estimate, PIC, or dependency to update] |

## Follow-Up

- [ ] Update affected frontend and backend tasks, PIC assignments, and estimates.
- [ ] Recalculate task and PIC manday totals after estimate changes.
- [ ] Recheck task scope against acceptance criteria and solutioning.

## SLA Baseline

Gunakan baseline Sprint 1 untuk task API kecuali ada target fitur yang lebih ketat: availability 99,9% per bulan; NFT 50 VUser / 20 request per detik; latency baca p95 ≤ 500 ms, tulis p95 ≤ 1 detik, p99 ≤ 2 detik; kapasitas awal 2 vCPU / 4 GiB per instance dengan CPU ≤ 70% dan memori ≤ 75%. Target beban dan kapasitas merupakan asumsi hingga divalidasi NFT.
