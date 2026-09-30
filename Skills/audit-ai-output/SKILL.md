---
name: audit-ai-output
description: "Framework for auditing AI-generated documents: grades on declared scope, classifies findings by actionability, and applies human-reality checks for time, capacity, and organizational friction."
metadata:
  compatible-agents: genie
---

# Audit AI Output

A reusable framework for judging AI-generated artifacts. Grades the document on what it claims to be, not what a maximally paranoid reviewer wishes it were. Separates real problems from scope creep. Accounts for how humans and organizations actually work.

## When to Use

Load this skill when:
- The user asks to audit, judge, or review an AI-generated document
- The user asks to build an audit prompt for a document
- The assistant is self-auditing its own output before delivering it
- Any document review where the goal is "tell me what's wrong, not what's missing from a different document"

## Step 1: Scope Lock

Before grading anything, answer these questions:

1. **What does the document say it is?** Read the title, subtitle, and opening paragraph. That's the declared scope.
2. **What document type is this?** Classify it:
   - **Status audit** (what exists, what's broken, what's at risk)
   - **Job map / JTBD** (what work must happen, who owns it, what depends on what)
   - **Execution plan** (how the work gets done, with runbooks, scripts, and schedules)
   - **Strategic doc** (why we should do this, what the opportunity is, what we're asking for)
   - **Technical spec** (how a system works, data models, API contracts, logic flows)
3. **Grade against the declared type.** A job map is not an execution plan. A status audit is not a strategic doc. Don't penalize a document for not being a different document.

### What each type owes the reader

| Type | Must include | Does NOT owe |
|------|-------------|-------------|
| Status audit | Accurate current state, identified risks, evidence-grounded assessment | Runbooks, load test plans, DR procedures |
| Job map / JTBD | Complete job list, owners, dependencies, sequencing | Detailed how-to for each job, cutover scripts, comms templates |
| Execution plan | Step-by-step procedures, rollback plans, test scripts, schedules | Strategic justification, alternative approaches |
| Strategic doc | Problem framing, opportunity sizing, ask, evidence | Implementation details, job lists, timelines |
| Technical spec | Data models, logic flows, API contracts, edge cases | Project management, owner assignments, timelines |

## Step 2: Human-Reality Checks

Before listing findings, run these checks. Each one catches problems that AI auditors consistently miss because they have no model for how people and organizations work.

### Time checks
- **Calendar time vs. effort time.** Filing a security review takes 30 minutes. Getting it approved takes 2-4 weeks. An approval dependency is a calendar-time dependency, not an effort dependency. If the document treats approvals as tasks, flag it.
- **Holiday and absence math.** Count the actual working days between now and the target date. Subtract holidays, offsites, team events, and known absences. If the document says "14 weeks" but 4 of those weeks are holidays/offsites, the real number is 10. Flag the gap.
- **Queue time for unengaged dependencies.** If someone is listed as "not engaged," add 2-4 weeks before any work flows through them. That's relationship-building time: explaining the ask, negotiating priority, getting it on their sprint.
- **Organizational speed limits.** Most organizations can process 1-2 major cross-team requests per week. If the document assumes 3 security reviews, 2 legal reviews, and an IT provisioning request all move in parallel in the same week, flag it as unrealistic.

### People checks
- **Owner concentration.** If any single person owns more than 5 parallel jobs, flag it as a capacity risk regardless of how small the jobs look. Context switching is expensive. 12 small jobs across 4 domains is harder than 3 large jobs in 1 domain.
- **Energy budgets.** Writing a threat model after a day of meetings is different from writing it on a deep-work day. If the document assigns cognitively demanding work to people with packed calendars, note it.
- **Contract and availability boundaries.** If a contractor's end date falls before their assigned deliverables complete, that's not a risk. It's a hard constraint. Name it as one.
- **The "not engaged" trap.** A name on a dependency list is not engagement. If the document lists someone as a dependency but notes they haven't been contacted, the dependency is fictional until someone has the conversation.

### Organizational checks
- **Approval chains are sequential, not parallel.** EDC before SDR before LPP. Each one has its own queue. Don't assume they run concurrently unless the organization explicitly allows it.
- **Cross-team work requires relationship capital.** Asking another team to do something costs political capital and calendar time. The document should acknowledge this, not just list the team as a dependency.
- **Scope creep in "missing items."** Before flagging something as missing, ask: does this belong in THIS document, or in a DIFFERENT artifact? If it belongs elsewhere, classify it as [NEXT], not [ADD].

## Step 3: Finding Classification

Every finding gets exactly one tag. No finding goes untagged.

- **[FIX]** — Wrong in this document. Factual error, incorrect dependency, stale date, misattributed owner. Fix before publishing.
- **[ADD]** — Missing from this document's declared scope. The document claims to cover X but doesn't. Add it to this document.
- **[NEXT]** — Real work that belongs in a different artifact. The document doesn't claim to cover this, and it shouldn't. Track it in a backlog or a follow-on document.
- **[NOISE]** — Prompt-seeded, pedantic, or aspirational. The auditor flagged it because the prompt told it to look for it, or because it's a nice-to-have that doesn't affect the document's fitness for purpose. Discard.

### How to tell [ADD] from [NEXT]

Ask: "If I handed this document to its intended reader, would they expect to find this item here?"

- If yes: [ADD]. The document is incomplete.
- If no: [NEXT]. The item is real but belongs elsewhere.

Example: A JTBD map that doesn't include a load testing job? [ADD] if the map claims to cover "everything required for production." [NEXT] if the map claims to cover "the major work streams."

### How to tell [ADD] from [NOISE]

Ask: "Would this finding exist if the audit prompt hadn't specifically asked about it?"

- If yes: [ADD]. The auditor found a real gap.
- If no: [NOISE]. The prompt manufactured the finding.

## Step 4: Grading

Grade on a simple rubric tied to the document type:

| Grade | Meaning |
|-------|---------|
| **A** | Does what it claims. No [FIX] items. Fewer than 3 [ADD] items. Human-reality checks pass. |
| **B** | Does what it claims with gaps. 1-3 [FIX] items or 3-5 [ADD] items. Minor human-reality issues. |
| **C** | Partially does what it claims. 4+ [FIX] items or significant [ADD] gaps. Human-reality checks fail on time or people. |
| **D** | Doesn't do what it claims. Major factual errors or missing sections. |
| **F** | Misleading or harmful. Would cause bad decisions if acted on. |

**The grade reflects fitness for purpose, not exhaustiveness.** A B+ job map that enables good decisions is more valuable than an A- execution plan that nobody reads.

## Step 5: Output Format

Structure the audit output as:

1. **Scope declaration** (1 sentence: what the document is and what it's graded against)
2. **Grade** (letter + 1-sentence justification)
3. **[FIX] items** (ranked by severity, with specific correction)
4. **[ADD] items** (ranked by impact, with suggested content)
5. **[NEXT] items** (grouped by target artifact, for backlog tracking)
6. **Human-reality flags** (time, people, and org issues found in Step 2)
7. **[NOISE] items** (listed briefly so the author knows what was considered and dismissed)

Do not summarize the document. The author wrote it. Tell them what's wrong, what's missing from their scope, and what belongs somewhere else.

## Anti-Patterns to Avoid

- **Don't grade a map as a plan.** A JTBD map that says "load test the system" is complete. A JTBD map that includes the load test script is overbuilt.
- **Don't manufacture findings from the prompt.** If the audit prompt says "check for X" and X isn't in scope, classify it as [NEXT] or [NOISE], not [ADD].
- **Don't treat every gap as equal.** A missing dependency chain is more important than a missing DNS configuration job. Rank by "would this cause a bad decision if the reader doesn't know about it?"
- **Don't ignore the human layer.** An audit that says "assign 12 jobs to one person" without flagging the capacity problem is a bad audit, regardless of how thorough the technical review is.
- **Don't confuse "the document doesn't mention it" with "the author didn't think of it."** Sometimes items are deliberately deferred. Ask before assuming.