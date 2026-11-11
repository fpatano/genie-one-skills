# Cognitive OS for Genie One

A set of Genie One skills that turn a blank assistant into one that knows who you are, works on a schedule, and gets better every night.

Skills hold the method. Memory holds you. Nothing personal lives in these files, so you can install them as-is and hatch your own assistant.

## What's in the box (v3, Cognitive OS 1.1.0)

| Skill | What it does | When to use it |
|-------|--------------|----------------|
| `cos-hatching` | Onboarding conversation. Sets your profile, priorities, tone, rhythm, and writes the default memories every other skill reads | Day 1. Start here |
| `cos-cognitive-loops` | Scheduled briefings, an 11pm thinking pass, optional 3am patterns, and a 4am self-improvement pass. Starts on a Light cadence of 4 tasks | After hatching |
| `cos-comms-sweep` | "What did I miss?" Sweeps Gmail and Slack into Direct, Project, and Ambient tiers | Week 1 |
| `cos-slack-dm-responder` | Reads your Slack DMs, researches the asks, drafts replies in your voice. Never sends on its own | Week 1, if Slack is connected |
| `cos-project-status-sync` | Reconciles your project memories and tracker with what actually happened | Week 2 |
| `cos-audit-ai-output` | Grades AI-written docs on what they claim to be, with human-reality checks for time, capacity, and org friction | Whenever you review AI output |

Two more capabilities get built for you during hatching from templates, not installed: a communication wrapper (your voice rules) and a priority triage.

## Install

**Option A: upload the files.** Download `cognitive-os/` (or `cognitive-os.zip` from the release). In Genie One, add each skill folder under your skills (each folder has a `SKILL.md`). Then open a new chat and say "hatch me."

**Option B: install a shared copy from Unity Catalog.** If someone on your team published these skills to a catalog and schema, open Customizations, then Skills, find the shared skills, and install each one. You need USE CATALOG, USE SCHEMA, and READ VOLUME on that schema.

Then:
1. Say "hatch me." It takes 3 to 15 minutes depending on how much you share.
2. Say yes when it offers the loops. You'll start on Light: a morning brief, a day close, an 11pm pass, and a 4am self-review.
3. After 3 to 5 days, tell it what's hitting and what's noise.

## Upgrading from v2 (`hatching-onboarding` + `cognitive-loop-system`)

1. Keep your memories. Don't re-hatch from scratch.
2. Install the six `cos-` skills.
3. Say "run cos-cognitive-loops Step 0." It checks your memory contract and writes anything missing, including the new `preferences/chat-memory-rules`.
4. Ask it to update your existing scheduled tasks from the new prompts. Don't create a second set.
5. Turn off the old v2 skills so two versions don't both trigger.

The v2 skills stay in `Skills/` for now so existing links keep working. They won't get updates.

## How it works

Sources (calendar, email, Slack, docs, meeting notes) feed memory. The loops read memory and sources on a schedule, filter for what changed that matters, and act: a briefing, a draft, a question. Every night the 4am pass reviews its own misses and tunes the memories.

Design rules:
- A skill is a reusable workflow, not a fancy prompt.
- Methodology in skills, personal context in memory.
- Silence is a valid result.
- A rule only changes behavior if it lives where the run reads it. Chat rules go in a broad memory; loop rules go in the task prompt.
- Start small. Earn the next cadence.

## Release notes

See `CHANGELOG.md`.
