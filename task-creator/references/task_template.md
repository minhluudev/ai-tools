# Task Template

Use this skeleton to draft task content before previewing it to the user (Step 5 in SKILL.md). Fill in every section — if information is missing, write "Not determined yet" / "Chưa xác định" (or "None" / "Không có") with a short reason, don't leave a section blank or delete it.

**Before using this template, make sure Step 0 (asking the user's preferred output language, and whether they want an estimate) has already been answered.** Write the title, all section content, and the section headers themselves in the chosen language, using the mapping table below.

## Section header mapping (Vietnamese ↔ English)

| # | Vietnamese | English |
|---|---|---|
| 1 | Bối cảnh | Context |
| 2 | Vấn đề hiện tại | Current Problem |
| 3 | Mong muốn / Kết quả cần đạt | Desired Outcome |
| 4 | Mục đích | Purpose |
| 5 | Phạm vi chỉnh sửa | Scope of Change |
| 6 | Tài liệu liên quan | Related Documents |

Use only one language's headers per task — never mix Vietnamese and English headers within the same task.

## Chat preview skeleton (shown here in English; substitute Vietnamese headers/wording if that's the chosen language)

Use this skeleton in Step 5, inside the chat, for the user to review. Do **not** save this literal text (with the emoji header) as a file — see the local-file skeleton below for that.

```
📋 PREVIEW TASK — {Target platform: Jira / Trello / Notion / Linear / Asana / local file / ...}

Title: {Action verb} + {specific affected object} — keep it short, ~6–10 words

Description:

### Context
{Current business/technical situation that led to this task}

### Current Problem
{What is specifically wrong/missing/suboptimal. For bugs: reproduction steps + environment + logs if available}

### Desired Outcome
{State after completion, stated clearly and measurably where possible — short acceptance criteria}

### Purpose
{Why this task is worth doing — benefit to the user/system/team}

### Scope of Change
{Modules/files/APIs/services expected to be touched. If unclear: "Needs further investigation once started"}

### Related Documents
{Links to design/spec/parent task/discussion thread/related PR. If none: "None"}

Estimate (only include this block if the user asked for one in Step 0 — omit entirely otherwise; complexity level: S / M / L / XL — see the reference table in SKILL.md):
- Complexity: {S/M/L/XL} — {short reason based on "Scope of Change" above}
- Coding (AI code): __ h — {short reason for this specific number}
- Code review: __ h — {short reason for this specific number}
- Self test: __ h — {short reason for this specific number}
- Total: __ h

Priority / Labels / Due date: {only include this line if the user mentioned one or the target board requires it — omit otherwise}

Assignee: {Not determined yet — ask the user in Step 6, don't guess}
```

## Local-file skeleton (used when writing `./docs/tasks/<ID>.md` — Step 7, Case B)

Once the preview above is approved and the assignee is confirmed, write the actual file using this cleaner format instead — no emoji header, small YAML metadata block up top so the file stays easy to scan/grep across many task files:

```markdown
---
id: <ID>
title: "{Approved title}"
assignee: "{Confirmed assignee}"
status: open
created: {YYYY-MM-DD}
estimate_hours: # omit this whole key if the user didn't want an estimate (Step 0)
  complexity: __ # S / M / L / XL
  coding_ai: __
  code_review: __
  self_test: __
  total: __
---

# {Approved title}

### Context
{...}

### Current Problem
{...}

### Desired Outcome
{...}

### Purpose
{...}

### Scope of Change
{...}

### Related Documents
{...}

## Estimate
<!-- Omit this entire "## Estimate" section if the user didn't want an estimate (Step 0). -->
- Complexity: {S/M/L/XL} — {short reason based on "Scope of Change" above}
- Coding (AI code): __ h — {short reason for this specific number}
- Code review: __ h — {short reason for this specific number}
- Self test: __ h — {short reason for this specific number}
- Total: __ h
```



## Notes on using this template

- Title: start with an action verb (Add, Fix, Optimize, Handle, Refactor, Update...); if the target board has its own convention (e.g. `[Bug]`, `[FEAT]` prefixes), follow that convention instead.
- If creating several tasks at once, repeat this skeleton for each task, numbered (Task 1, Task 2, ...), and combine them into a single preview batch before asking for confirmation.
- The "Assignee" field always stays "Not determined yet" in the initial preview — only fill it in after the user answers in Step 6; don't put it in the preview as an assumption.
