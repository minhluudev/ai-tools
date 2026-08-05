# Estimate Complexity Scale

Reference table for Step 4 in SKILL.md. Only used when the user asked for a time estimate in Step 0.

Derive the estimate from the complexity of the **Scope of Change** drafted in Step 3, using the table below as a starting point, then adjust for real-world factors (number of systems involved, whether there's a DB migration, dependencies on other teams, complex UI, etc.).

| Complexity | Typical characteristics | Coding (AI code) | Code review | Self test |
|---|---|---|---|---|
| **S — Small** | 1 file/1 function, simple logic, no schema/API contract change | 0.5 – 1h | 0.5h | 0.5h |
| **M — Medium** | 2–5 files, 1 module, may add/change 1 API, no major migration | 1 – 3h | 1 – 1.5h | 1 – 2h |
| **L — Large** | Multiple related modules/services, has a DB migration or a contract change affecting other places | 3 – 8h | 1.5 – 3h | 2 – 4h |
| **XL — Very large** | Affects multiple services/teams, requires redesigning a flow, high risk | 8h+ (should be split into smaller tasks) | 3h+ | 4h+ |

## Rules

- If the estimate lands in the **XL** range, proactively suggest the user split the task into smaller (S/M) subtasks instead of keeping one giant task. When you do split it, link parent and children explicitly: put the parent task's link/ID in each subtask's **Related Documents** section, and list the subtasks' links/IDs in the parent's Related Documents section once they exist — don't leave the relationship implicit.
- **Every number must come with its own short reason, not just one general sentence.** State the complexity level chosen (S/M/L/XL) and why (based on the scope of change), then for each of the 3 numbers, give a short phrase for why that specific figure was picked — especially when it deviates from the table's default range (e.g. "Code review: 2h — touches a shared auth module, needs a second reviewer's sign-off" rather than just "M complexity"). Never give a number without a reason attached to it.
- If the scope information isn't sufficient for an accurate estimate, say clearly that this is a preliminary estimate that may change after further investigation.
