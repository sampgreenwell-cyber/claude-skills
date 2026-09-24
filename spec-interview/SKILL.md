---
name: spec-interview
description: Interview the user through a seven-part spec (Outcome, Boundary, Context, Constraints, Evidence, Verification, Stop points), then turn the answers into an OpenSpec change proposal, the same result as /opsx:propose. Use this whenever the user says "interview me", "spec interview", "help me write a spec", or wants to plan a feature or change before building it in a repo that uses OpenSpec.
---

# Spec Interview → OpenSpec Proposal

Interview the user one section at a time, then create an OpenSpec change from the answers.

## Rules for the interview

- Ask about **one section at a time**, in order 01 → 07. Do not dump all seven questions at once.
- Before starting, ask one opening question: "What do you want to build or change? One or two sentences is fine." Use the answer to tailor every later question.
- For each section, ask the core question below, plus at most one follow-up if the answer is vague. Offer 1–3 concrete suggestions based on the codebase and earlier answers so the user can react instead of starting from blank.
- Push for specifics. If an outcome can't be observed or a verification can't be run, say so and help sharpen it.
- The user may answer "skip" or "not sure". Record it as an open question rather than inventing an answer.
- Never invent facts about the codebase. Anything you state about existing code must come from actually reading it.

## The seven sections

**01 Outcome** — What has to be *observably* true when we're done?
Look for: end states someone could see or check, not tasks. Rewrite "implement X" into "a user/client can …".

**02 Boundary** — What's in? And *explicitly* what's out?
Always ask for the "out" list, and suggest likely scope creep the user may want to exclude.

**03 Context** — Why are we doing this, and what prior decisions does it inherit?
Suggest decisions you can see in the repo (framework, patterns, storage, conventions) and ask the user to confirm them.

**04 Constraints** — What must remain true, no matter how it gets built?
Examples: existing endpoints keep working, no new dependencies, container still builds, performance limits.

**05 Evidence** — What exists today? Research, code, behavior, screenshots, failures.
Do the legwork here: read the relevant code first, then present what you found (file paths, current behavior) and ask the user to confirm or add to it (logs, screenshots, bug reports).

**06 Verification** — What can we run or observe to *prove* it worked?
Each outcome from 01 needs at least one check: a test, a command with expected output, a build that succeeds.

**07 Stop points** — Where does the system stop guessing and come back to a human?
Suggest sensible defaults (before adding dependencies, before changing a public contract, when requirements conflict) and ask what else the user wants to approve personally.

## After the interview

1. Show a compact summary of all seven sections, with any open questions listed at the end. Ask: "Anything to change before I create the proposal?" Wait for confirmation.
2. Pick a short kebab-case change name (e.g. `add-health-checks`) and confirm it with the user.
3. Create the OpenSpec change by following this project's OpenSpec propose workflow: look in `.claude/skills/` and `.claude/commands/` for the installed OpenSpec propose instructions (the ones behind `/opsx:propose`) and follow them exactly, including any `openspec` CLI commands they use. Use the interview answers as the input instead of asking the user again.
4. Map the answers into the OpenSpec artifacts:
   - **proposal.md**: Context (why), Boundary (in/out scope), and Outcome summary. Also add a `## Stop Points` section and a `## Open Questions` section, since OpenSpec has no standard home for these.
   - **specs/**: each Outcome becomes a requirement with scenarios (WHEN/THEN). Include Verification checks as scenarios where they fit.
   - **design.md**: Constraints and Evidence, plus the technical approach.
   - **tasks.md**: the implementation steps, with Verification checks as explicit tasks (write/run tests, run commands). Add a first task: "Re-read Stop Points in proposal.md".
5. Run `openspec validate <change-name> --strict` if available and fix any problems.
6. Tell the user where the files are and that the next step is to review them, then run `/opsx:apply`.

## During implementation

If this change is later applied, treat the Stop Points in proposal.md as hard rules: when one is reached, stop and ask the user before continuing.