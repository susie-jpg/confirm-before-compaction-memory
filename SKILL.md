---
name: confirm-before-compaction-memory
description: Ask the user to review and confirm candidate conversation memories for removal before context compaction; preserve goals, constraints, decisions, and unresolved work.
---

# Confirm Before Compaction Memory

Use this skill when a conversation is becoming long, a context compaction/summarization is imminent, the user asks to clean up memory, or the user explicitly invokes this skill.

## Core rule

Do not silently discard potentially useful conversation state. Before any compaction that this agent can anticipate, stop and present a review checkpoint. Ask the user to confirm which candidate items may be removed. Do not treat a generic reply such as "continue" as permission to remove items unless the candidate list was just shown and the user clearly approved that list.

The skill governs the agent's workflow; it cannot create a hook into a host application's hidden compaction process. If the host begins compaction without giving the agent a turn, preserve a compact checkpoint as early as possible and state that confirmation could not be obtained. Never claim that the skill prevented an already-started host compaction.

## Before compaction

1. Build a checkpoint from the conversation. Separate it into:
   - **Protected**: current objective, explicit user requirements, hard constraints, confirmed decisions, important identifiers, acceptance criteria, unresolved bugs, and next actions.
   - **Candidates for removal**: duplicate explanations, superseded approaches, completed intermediate steps, stale search results, rejected alternatives, and conversational filler.
   - **Uncertain**: anything whose relevance is unclear. Treat this as protected by default.
2. Do not propose removing secrets, personal data, source-of-truth files, or user preferences merely because they are old. Redact secrets from the checkpoint rather than copying them into a new summary.
3. Show the candidate list with stable IDs and a short reason for each item. Show the protected and uncertain sections as well, so the user can catch omissions before confirming.
4. Prefer a choice-based confirmation UI when the host provides one. Present each candidate with two choices: **Keep** and **Remove**, with **Keep** as the default. If no choice UI is available, render the same interaction as numbered options, for example:
   - `C1`: Keep / Remove
   - `C2`: Keep / Remove
   - `C3`: Keep / Remove
   Also offer global choices such as **Keep all**, **Remove only selected**, and **Cancel**.
5. Wait for an unambiguous response. If the user changes a choice, use the latest explicit selection. A generic reply such as "continue" selects **Keep all**, never removal. If the user does not answer or the answer is ambiguous, preserve all candidates and do not proceed with intentional removal.

## After confirmation

Compact only the items the user explicitly approved for removal. Preserve the remaining protected, uncertain, and unapproved candidate items in the new working summary. Start the next turn with a short **Memory checkpoint** containing:

- objective and success criteria;
- protected constraints and confirmed decisions;
- unresolved work and next actions;
- the IDs of approved removals;
- assumptions that still need confirmation.

Do not imply that this summary is durable cross-session memory. When the user or environment provides a persistent state file, database, or memory tool, write only the approved state through that mechanism and follow its existing permission rules. Otherwise, keep the checkpoint in the conversation and tell the user it is session-scoped.

## Normal operation

Do not interrupt every ordinary turn. Maintain a lightweight internal distinction between protected state and disposable narrative, and trigger the review only when compaction is imminent, requested, or reasonably likely. If there is no visible compaction signal, proactively offer a checkpoint at a natural milestone for long-running work, but do not claim that compaction is definitely about to happen.
