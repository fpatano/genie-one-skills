---
name: cos-slack-dm-responder
description: "Use when the user says 'check my DMs', 'help me reply to [person]', or 'what did [person] send me'. Reads their Slack DMs, finds resource requests and technical questions, researches answers, and drafts replies in their voice. Never sends without approval."
metadata:
  compatible-agents: genie
  version: "1.1.0"
  suite: cognitive-os
---

# Cognitive OS: Slack DM Responder

Reads the user's inbound Slack DMs, finds the ones asking for a resource or technical help, researches the answer, and drafts a reply in the user's voice. Draft first, always.

## Step 0: Load Context

- `reference/comms-channels`: the user's Slack user ID. If missing, look it up with the Slack user search tool and save it there.
- `profile` and `projects/*`: what the user works on, so drafts reflect their actual expertise and resources
- The user's `communication-wrapper` skill if it exists. Otherwise `preferences/communication-style`.

## Message Types

### 1. Resource requests
Someone wants a link, repo, doc, deck, video, or asset.

Research:
1. Search the user's own Slack messages (`from:<@SLACK_USER_ID>`) for the resource
2. Check `projects/*` memories for related links
3. Search Drive for docs and decks
4. Search GitHub if it's a repo and GitHub is connected
5. Include companion resources (a related blog, video, or upcoming session) when they exist

### 2. Technical questions
"How does X work," "does the product support Y," "what's the guidance on Z."

Research:
1. Search the user's past Slack answers on the topic
2. Check workspace assets if it's a data question
3. Search product docs
4. Check `projects/*` for context
5. Draft at the user's depth, not a generic answer

### 3. Out of scope: flag, don't draft
Review requests, scheduling, personal or social messages, status updates that need the user's judgment, or anything that commits the user's time or makes a decision.

> "This one needs you. [Person] is asking: [summary]. Want help with any piece of it?"

## Flow

1. **Read the DM.** `slack_read_channel` with the sender's user ID, last 15 messages. Find the latest inbound message that needs a reply.
2. **Classify** into one of the three types. Out of scope: summarize and stop.
3. **Research** per the type.
4. **Draft** in the user's voice:
   - Casual Slack register. Lead with the answer, then context and extra links.
   - Match length to the ask. A simple ask gets a short answer.
   - Follow the user's communication-wrapper or communication-style rules.
   - Any time, date, or meeting in the draft comes from a live calendar read, not memory.
   - Before naming someone's team or role, check the people directory.
   - Frequency or scope claims ("one-off," "rare," "everyone sees this") need evidence. Otherwise say it's unknown.
5. **Present:** who sent it, what they asked, the draft, then: "Send it, tweak it, or want to handle this one?"

Never send without explicit approval.

## Getting Better Over Time

Track which drafts the user sends as-is and which they rewrite. When they rewrite, add the lesson to their `communication-wrapper` Known misses in the same turn. When send-as-is is consistently high for a message type, it's reasonable to propose expanding scope (for example, scheduling replies with a calendar check).
