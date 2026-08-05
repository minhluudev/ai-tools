---
name: task-creator
description: Use when the user wants to create, draft, or push a task/ticket/issue to a task management tool (Jira, Trello, Notion, Linear, Asana, monday.com, etc.), or wants it recorded locally when no such tool is available. Trigger on phrases like "create a task/ticket/issue", "log this as a task", "push this to Jira/Notion/Trello", or when the user describes a bug/feature/piece of work that needs to be tracked — even without using the word "task".
metadata:
  author: Minh Luu
---

# Task Creator

Skill for drafting and creating tasks on task management tools (Jira, Trello, Notion, Linear, Asana, monday.com...) following a consistent content standard, with an objective estimate, and always reviewed by the user before the real task is created.

## Core principles

1. **Never create the real task immediately.** Always draft content → preview for user approval → ask for assignee → only create on the real system (or write the local file) after the user explicitly confirms ("ok", "go ahead", "looks good", ...). Creating a task on Jira/Trello/Notion — or writing a task file into the local project — writes data to the user's/team's system, so it always requires confirmation before execution (submit).
2. **If no task management tool is available, fall back to a local Markdown file** at `./docs/tasks/<ID>.md` instead of giving up — see Step 7, Case B.
3. **The estimate is optional and only produced if the user asks for it.** Ask before drafting anything (Step 0) — if the user doesn't want an estimate, leave the Estimate section out of the preview and the final task/file entirely rather than filling it with placeholders. When an estimate is wanted, it must be objective, based on the actual complexity of the task, not a guess — use the reference table in Step 4 to reason about it, never give a round number "just because."
4. **The description must always contain all 6 sections** in the exact order below, never skip a section (if information is missing, ask the user or write "Not determined yet" and note that it needs follow-up).
5. If the user gives too little information for a section, ask a short follow-up question in chat — offer short answer choices when the question naturally has a small set of options, otherwise ask as free text.
6. **Ask which language the task content should be written in** (Vietnamese or English) before drafting the title/description — see Step 0. Do not assume based on the language the user is chatting in; ask explicitly, since the target platform/team may use a different language for tickets than the conversation.

## Reference files

- `references/task_template.md` — the standard skeletons for drafting task content: a chat-preview skeleton (Steps 3–5) and a separate local-file skeleton for the `./docs/tasks/<ID>.md` fallback (Step 7, Case B), plus the Vietnamese/English section-header mapping table. Read this file when drafting a preview or a local file, instead of inventing a different structure.
- `references/examples.md` — 3 fully worked examples at 3 complexity levels (S/M/L), written in Vietnamese for illustration, including how the estimate reasoning works. Read this when you need a reference for the right level of detail per section, or when the user wants to see an example before drafting their own task. If the user's chosen output language is English, translate the structure/level of detail shown, not the literal Vietnamese wording.
- `references/estimate_scale.md` — the S/M/L/XL complexity table and reasoning rules for Step 4. Read this whenever the user asked for an estimate, instead of guessing hours from memory.

## Step-by-step process

### Step 0 — Determine output language and estimate preference

Before drafting any content, ask the user two things up front, in a single chat message (both questions together) rather than two separate round trips:
1. Whether the task title/description should be written in **Vietnamese** or **English**. Skip asking if an earlier message already made this clear. This choice is independent of the language the user is chatting in — don't assume based on that.
2. Whether they want an objective time estimate for this task (Yes/No) — see Step 4 for what happens with each answer.

If the user is asking you to create **several tasks in the same request** (e.g. from a list of work items), ask this once for the whole batch, not once per task.

Use the chosen language consistently for the title, all 6 description sections, and the section headers themselves (see the mapping table in `references/task_template.md`).

### Step 1 — Gather information

From the user's request (bug description, feature idea, chat excerpt, etc.), extract or ask for enough to have:
- Target platform: Jira, Trello, Notion, or other — or a local Markdown file under `./docs/tasks/` if no tool is available/connected (ask if unclear). Check the connected tools list early so the target platform stated in the preview is accurate, instead of only discovering this in Step 7.
- Relevant business/technical context.
- The problem being faced (if it's a bug/pain point).
- The desired outcome.
- The code/feature scope affected (if known).
- Related documents/links (design, spec, parent ticket, Slack thread...).

If the user pastes a long conversation/content, summarize it yourself and extract the key points into the right sections — don't re-ask for things already present in that content.

### Step 2 — Draft the Title

Title rules:
- **Brevity is the top priority.** Aim for ~6–10 words, hard cap at 12. A title is a label to scan in a list, not a mini-description — if it's running long, move the detail into the description instead of stuffing it into the title.
- Start with an action verb (Add, Fix, Optimize, Handle, Refactor, Update...) or follow the `[Type] Content` convention if the team uses a type prefix (Bug/Feature/Improvement/Chore) — ask the user if the target board's convention is unclear.
- Name the affected object concretely, but keep it to the object itself — skip context, cause, or acceptance criteria; those belong in the description, not the title. Also avoid vague phrasing like "Fix bug", "Update stuff" that names nothing concrete.
- If creating on a board/project that already has many tasks and you have a way to look up recent tasks on that board, check a few of them to match the existing naming convention before drafting; otherwise draft using the general rules above.

### Step 3 — Draft the Description (all 6 sections, in order)

1. **Context** — The current business/technical situation that led to this task. Helps the reader (another dev, a reviewer) understand "why" the task exists.
2. **Current Problem** — What is specifically wrong/missing/suboptimal. For bugs: include reproduction steps, environment, logs if available.
3. **Desired Outcome** — The state after the task is completed, stated clearly and measurably where possible (short acceptance criteria).
4. **Purpose** — What problem this task solves for the user/system/team; why it's worth doing.
5. **Scope of Change** — The specific modules/files/APIs/services expected to be touched. If unclear, write "Needs further investigation once started" instead of leaving it blank. If you have file/code access to the relevant repository, it's worth briefly inspecting the actual code (e.g. `view`/`bash` a few relevant files or directories) to confirm which files/modules are really involved, rather than relying only on the user's description — this makes both this section and the Step 4 estimate more accurate. Don't do a full deep audit; a quick, targeted look is enough.
6. **Related Documents** — Links to design docs, specs, parent tasks, discussion threads, related PRs... If none, write "None".

If a section genuinely doesn't apply, keep the section and give a short reason (e.g. "No related documents at the time this task was created") — don't delete the section.

**Optional extra fields (Priority, Labels/Type, Due date):** many real boards use these. Don't ask about them by default — only include them if the user mentions one, or if the target platform's board clearly requires one (e.g. Jira project mandates a Priority field) and you can't create the task without it. When included, add them as a short line after the 6 sections (e.g. "Priority: High"), not as a 7th mandatory section.

### Step 4 — Draft the estimate objectively, if the user wanted one (unit: hours)

If the user said "No" to the estimate question in Step 0, skip this step entirely — the preview and the final task/file must not contain an Estimate section at all (don't fill it with placeholders or zeros). If they said "Yes", continue below.

The estimate has 3 separate parts, never merged:
- **Coding (AI code)** — time for AI code (Claude Code/Copilot...) to generate the code for the task, including the time spent by the person coordinating prompts/quickly checking between steps.
- **Code review** — time for a reviewer to read, understand, check logic, and request changes.
- **Self test** — time for the implementer to test it themselves (manual testing, running test cases, checking on a staging environment...).

Derive the estimate from the complexity of the **Scope of Change** drafted in Step 3. Read `references/estimate_scale.md` for the S/M/L/XL reference table (typical hours per part) and the rules for reasoning about each number, splitting XL tasks, and handling insufficient scope information — don't invent a different scale.

### Step 5 — Preview (mandatory, before creating the real task)

Before calling any tool to create the task, always show the full content as a preview in chat for the user to review. Use the exact skeleton in `references/task_template.md` (read that file to get the standard format — don't invent a different one), including or omitting the Estimate block depending on what the user answered in Step 0. Check `references/examples.md` if you need a reference for the appropriate level of detail per section.

Ask explicitly: "Is this content good to create the task, or would you like to change anything?" Only move to Step 6 once the user confirms they're happy with it, or has finished editing and agrees.

### Step 6 — Ask for the Assignee

Once the content is approved, always ask who the task should be assigned to before creating it (don't guess, and don't default to "assign to me" unless the user said so).
- **Case A (real tool):** if you have a way to look up the target workspace's user list, use it to suggest names for the user to pick from, instead of making them recall the exact username/email. If more than one workspace member matches the name given, show the matches and ask the user to pick the right one rather than guessing. If no such lookup is available, just ask the user for the name/handle directly, same as Case B.
- **Case B (local file fallback):** there is no workspace to look up — simply ask the user for a name/handle to record as the assignee (free text), no lookup needed.

### Step 7 — Create the task on the target platform (or as a local file)

Only proceed once you have (a) content approved in Step 5 and (b) an assignee confirmed in Step 6.

**Case A — A task management tool is available.**
- Check whether a connector/integration for the target platform (Notion, Atlassian/Jira, Trello, Linear, Asana, monday.com...) is already available among the tools you currently have access to.
- If no suitable connection exists yet, tell the user which platform you'd need a connection for and ask them to connect/enable it (however connectors are set up in the environment you're running in), rather than picking a platform on their behalf or guessing at a workaround.
- If the user has already specified the platform/connector, use it directly.
- Before creating, especially for bug reports, it's worth a quick check for an existing similar/duplicate task on the target board (e.g. search by a few keywords from the title) — if you find a likely duplicate, point it out and ask the user whether to proceed anyway, rather than silently creating a duplicate ticket. Skip this check if the board is large/unindexed or the search tool isn't available — don't block on it.
- When creating the task: fill in the title, all 6 description sections (keep the headings for readability on the target platform), attach the estimate to the appropriate field if the user opted for one in Step 0 (a custom field if the platform supports one, otherwise note it in the description), and assign the confirmed assignee from Step 6.
- After creation, return the link/ID of the newly created task to the user.
- If the tool call fails because of an auth/credential error, don't silently give up or retry blindly — surface the exact error to the user, ask them to re-authenticate/reconnect that platform's connector, then retry once they confirm it's fixed.

**Case B — No task management tool is available (fallback: local file).**
If there is no connected task management tool and the user doesn't want to connect one (e.g. they decline when asked, or explicitly say to just save it locally), create the task as a Markdown file in the current local project instead, at:

```
./docs/tasks/<ID>.md
```

Resolve this path relative to the **project root** (e.g. the directory containing `.git`, or the top-level folder the user opened), not the current working subdirectory — otherwise tasks can end up scattered across multiple `docs/tasks/` folders in different subdirectories, breaking the sequential ID scheme. If the project root isn't obvious, ask the user rather than guessing.

- **Determine `<ID>`**: list the existing files in `./docs/tasks/` (create the directory if it doesn't exist yet) and use the next sequential number, zero-padded to 3 digits (`001`, `002`, `003`, ...). Never guess the next ID without checking existing files first — if the directory already has `007.md`, the new file must be `008.md`. When creating **several files in one batch**, compute the whole run of IDs up front from a single directory listing (e.g. `008`, `009`, `010`, ...) rather than re-listing the directory after writing each file — re-listing mid-batch risks reusing an ID if the write hasn't landed yet.
- **File content**: use the local-file skeleton in `references/task_template.md` (not the chat preview skeleton — that one has a "📋 PREVIEW TASK" header meant for the chat, not for a saved file). Carry over the exact approved title, all 6 description sections, estimate, and assignee from the approved preview — don't reformat or shorten the content itself, just drop it into the cleaner file skeleton (with its small YAML metadata block).
- After creating the file, tell the user the path of the file that was created (e.g. `./docs/tasks/008.md`) so they can find it.
- This local-file fallback still requires the same approval flow as Case A: draft → preview (Step 5) → assignee (Step 6) → only then write the file.
- If the user later connects a real task management tool, offer to migrate the content of existing local task files into real tasks on that tool rather than leaving them stranded as files.

## Scope

This skill covers **creating** new tasks only — drafting, previewing, and pushing them to a platform or local file. It does not cover updating, closing, re-assigning, or changing the status of a task that already exists; for that, use the target platform's connector/tools directly, since those flows (status transitions, workflow rules) vary a lot by platform and aren't standardized here.

## Notes

- Don't automatically create a task multiple times for the same request unless the user has clearly confirmed each time.
- If the user asks to create several tasks at once (e.g. from a list of work items), draft previews for **all** tasks first so the user can review them in bulk, then create them one by one — avoid interleaving drafting and creating, which is easy to mix up.
- Keep the description's writing style concise and clear, using active voice; avoid restating the user's request verbatim without distilling it.
- If a task creation call fails for any reason other than auth (e.g. invalid field, missing required project field on the target board), report the exact error to the user and ask how to proceed — don't silently drop fields to force it through, and don't retry more than once without the user's input.
