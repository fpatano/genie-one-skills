---
name: cos-audit-ai-output
description: "Use when the user asks to audit, grade, or review an AI-generated document, wants an audit prompt built, or before delivering a long AI-written artifact. Grades on declared scope, tags findings FIX/ADD/NEXT/NOISE, and checks for human time, capacity, and org friction."
metadata:
  compatible-agents: genie
  version: "1.1.0"
  suite: cognitive-os
---

# Cognitive OS: Audit AI Output

Grades an AI-written document on what it claims to be, not on what a maximally paranoid reviewer wishes it were. Separates real problems from scope creep, and accounts for how people and organizations actually work.

## When to Use

- The user asks to audit, judge, or review an AI-generated document
- The user wants an audit prompt for a document
- Self-check before delivering a long AI-written artifact

## Step 1: Scope Lock

1. **What does the document say it is?** Title, subtitle, opening paragraph. That's the declared scope.
2. **What type is it?** Status audit, job map, execution plan, strategic doc, or technical spec.
3. **Grade against that type.** Don't penalize a document for not being a different document.

| Type | Must include | Does NOT owe |
|------|-------------|--------------|
| Status audit | Accurate current state, risks, evidence | Runbooks, test plans, DR procedures |
| Job map | Complete jobs, owners, dependencies, sequence | How-to per job, cutover scripts |
| Execution plan | Steps, rollback, tests, schedule | Strategic justification |
| Strategic doc | Problem, opportunity, ask, evidence | Implementation detail, job lists |
| Technical spec | Data models, flows, contracts, edge cases | Owners, timelines |

## Step 2: Human-Reality Checks

AI auditors miss these because they have no model of how people work.

**Time**
- **Calendar time vs effort time.** Filing a review takes 30 minutes. Getting it approved takes weeks. Approvals are calendar dependencies, not tasks.
- **Holiday and absence math.** Count real working days to the target. Subtract holidays, offsites, and known absences.
- **Queue time.** Someone listed as "not engaged" adds 2 to 4 weeks before work flows through them.
- **Org speed limits.** Most orgs absorb 1 or 2 major cross-team requests a week. Five parallel reviews in one week isn't real.

**People**
- **Owner concentration.** More than 5 parallel jobs on one person is a capacity risk, however small the jobs look.
- **Energy.** Deep work assigned to someone with a packed calendar won't happen on schedule.
- **Hard boundaries.** A contractor whose end date falls before their deliverables is a constraint, not a risk.
- **The "not engaged" trap.** A name on a dependency list isn't engagement until someone has had the conversation.

**Organization**
- **Approval chains usually run in sequence.** Security, legal, and privacy reviews each have their own queue. Don't assume they overlap unless the org allows it.
- **Cross-team asks cost relationship capital.** The document should acknowledge it.
- **Scope creep.** Before calling something missing, ask whether it belongs in this document or a different one.

## Step 3: Tag Every Finding

- **[FIX]**: wrong in this document. Factual error, wrong dependency, stale date, wrong owner.
- **[ADD]**: missing from this document's declared scope.
- **[NEXT]**: real work that belongs in another artifact.
- **[NOISE]**: seeded by the audit prompt, pedantic, or aspirational. Discard.

ADD or NEXT? "Would the intended reader expect to find this here?" Yes is ADD. No is NEXT.
ADD or NOISE? "Would this finding exist if the prompt hadn't asked for it?" Yes is ADD. No is NOISE.

## Step 4: Grade

| Grade | Meaning |
|-------|---------|
| A | Does what it claims. No FIX. Fewer than 3 ADD. Reality checks pass. |
| B | Does what it claims with gaps. 1 to 3 FIX or 3 to 5 ADD. Minor reality issues. |
| C | Partly does what it claims. 4+ FIX or big ADD gaps. Reality checks fail on time or people. |
| D | Doesn't do what it claims. |
| F | Misleading. Would cause bad decisions. |

The grade is fitness for purpose, not exhaustiveness.

## Step 5: Output

1. Scope declaration (1 sentence)
2. Grade and 1-sentence reason
3. FIX items, by severity, with the correction
4. ADD items, by impact, with suggested content
5. NEXT items, grouped by target artifact
6. Human-reality flags
7. NOISE items, briefly

Don't summarize the document. The author wrote it.

## Anti-Patterns

- Grading a map as a plan
- Manufacturing findings from the prompt
- Treating every gap as equal
- Ignoring the human layer
- Assuming "not mentioned" means "not thought of." Sometimes it was deliberately deferred. Ask.
- Folding on pushback. When the user disputes a finding, re-read the source and answer with the evidence, whether that means dropping the finding or keeping it.
- Presenting your own score or proxy as the document's data. Label it yours.
