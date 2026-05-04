---
name: reasoning-pass
description: |
  Generic reasoning pass skill for ad-hoc validation of artifacts. For software factory
  workflow steps, use the specialized sf-reasoning-* skills instead. This skill serves
  as the base pattern and fallback for non-workflow reasoning needs.
license: MIT
compatibility: pi-agent
---

## When to Use This Generic Skill

- Ad-hoc artifact validation outside software factory workflow
- Custom artifacts not covered by specialized skills
- Debugging / investigation of existing artifacts
- Manual quality checks requested by user

## Reasoning Pattern (DECOMPOSE-SOLVE-VERIFY-SYNTHESIZE-REFLECT)

### Step 1: Assess Complexity

Read the full artifact. Classify as:
- **Simple**: Clear scope, few integration points, well-understood domain
- **Complex**: Multiple integration points, cross-cutting concerns, novel domain

### Step 2a: Lightweight Pass (Simple)

1. **Gap check**: Are there obvious edge cases not covered? Flag explicitly.
2. **Contradiction check**: Do any items conflict with each other?
3. **Reference completeness**: Are referenced files/docs/IDs present?

Assign confidence (0.0-1.0). If >= 0.8, proceed. If < 0.8, continue to loop.

### Step 2b: Full Pass (Complex)

#### DECOMPOSE
Break into sub-problems with:
- Clear label
- Category: **quality** (completeness, ambiguity, contradictions) or **design** (approach, edge cases, risks)
- Confidence score (0.0-1.0)

#### SOLVE
For each sub-problem with confidence < 0.8:
- Identify what is missing, ambiguous, or contradictory
- Note specific items involved

#### VERIFY
- Do any sub-problem solutions conflict?
- Are cross-cutting concerns addressed?

#### SYNTHESIZE
- Compute overall confidence (weighted average by criticality)
- Produce reasoning summary

#### REFLECT
Attempt auto-resolution from context. Present proposals to human:
> "Based on [source], I propose resolving [gap] as: [resolution]. Do you agree?"

### Step 3: Follow-Up Resolution Loop

1. Identify the **single most impactful** unresolved gap
2. Ask exactly **one** targeted question
3. Wait for response
4. Re-run reasoning loop, overwrite output
5. If confidence >= 0.8, proceed
6. If still < 0.8, offer choice:
   > "Confidence is [score]. Continue resolving or proceed anyway?"

**Human can say "skip" at any point.**

### Step 4: Write Output

Write reasoning output to appropriate destination based on artifact type.

## What NOT to Do

- Do NOT ask multiple questions in one turn
- Do NOT silently add items the human didn't confirm
- Do NOT append multiple reasoning outputs (always overwrite)
- Do NOT prevent the human from proceeding (always allow skip)
- Do NOT reference files or IDs that don't exist
