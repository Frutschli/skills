---
name: unslop
description: Use when cleaning up a vibecoded codebase that grew messy under AI agents — the same logic implemented several ways, dead code with no callers, tests guarding removed functionality, leftover artifacts, or two places encoding conflicting ideas of the same thing. Use when the user says "unslop", "clean this up", or wants to shrink the mess rather than add more. For restructuring shallow modules into deep ones, use improve-codebase-architecture instead.
---

# Unslop

Find the **exact locations** where a vibecoded codebase has rotted, and grill through how to remove them. Assume user only knows what the code does conceptually; this finds *why the same idea keeps appearing*, where it belongs, and how to shrink the surface.

This is the removal sibling of `improve-codebase-architecture` (IcA). IcA makes shallow modules deep — restructuring, where complexity moves and line count can grow. **`unslop` only shrinks surface**: delete, collapse N→1, finish an abandoned migration. The test for every finding — *does this make the surface smaller?* If it just moves or restructures complexity, it's IcA's job, not this skill's.

**Assume everything is vibecoded.** Existence is not justification.

## Prime directive

Critical twice — of the code and of yourself.

A fix that does the same thing a different way is **churn** — motion without removal — and churn is slop; discard it instantly. Don't kick it down the road or leave a smaller version of the same mess. No genuinely better idea → say so; an honestly unresolved smell beats an invented worse one. Never propose a change just to have one.

A smell is an assumption until you fail to disprove it. Hunt for the caller you claim is absent; check that two things you call "the same" actually change together. Earn the right to call something dead or duplicate.

Two gates before any fix — pass both or it's slop (discard) / `Unresolved`:

- **Surface gate** — does it *remove* call sites, files, branches, exports? Count them. Net zero = **churn** → discard.
- **Same-concept gate** — for any N→1 collapse, the N must change together when the concept changes. If not, merging only couples unrelated code → discard.

## The rot (common shapes, not a checklist — explore organically)

- **Scattered concept** — one thing in N places; dangerous case is the same idea expressed differently each time, so callers disagree subtly.
- **Dead code** — no callers, unreachable branches, orphaned modules.
- **Conflicting concepts** — two places encode contradictory ideas of the same thing; usually a migration never finished everywhere.
- **Dead tests** — tests targeting removed/dead functionality. They pass, so no one notices they guard nothing.
- **Artifacts** — leftover files, commented-out blocks, stubs, abandoned TODOs, unwired scaffolding.

## Process

**1. Context.** Search for `CONTEXT.md` (also `context.md`; `CONTEXT-MAP.md` for multi-context repos). Present → read first, anchor the hunt to its vocabulary. Absent → skip silently, don't create one.

**2. Hunt.** Default: whole codebase. User named an area/concept → scope to it, follow the rot outward. **Scout, don't verify:** `Explore` subagents return candidate `file:line`s only; the verdict — gates, disproof — never leaves the main thread, so the grill can interrogate the evidence directly. "Dead" is the trap unique to a removal skill: assume code is live, earn "dead" by *failing* to find any path — framework wiring, reflection/string keys, exports, entry points, tests. Indirection you can't rule out → downgrade to a grill question, never a delete.

**3. Present as markdown** (no HTML, no report file). Cap the list; **no floor** — show only what passes both gates, one finding is fine, none is a valid result (then show what you looked at and why each isn't slop). Never pad to a number. One card per finding:

- **Smell** — type + one line, in `CONTEXT.md` vocabulary where it exists
- **Locations** — `file:line`, every instance
- **Why it's slop** — evidence, not vibe: the absent caller, the divergent logic, the contradiction — and what you did to try to disprove it
- **How the surface shrinks** — what gets deleted/collapsed, the single home. Or `Unresolved — needs your input` with the tension stated.
- **Confidence** — `Strong` (gates cleared) / `Worth exploring` / `Speculative` (indirection or same-concept doubt remains → it's a grill question, not a fix)

End with a **Top recommendation**. Do NOT edit code. Present, then grill.

**4. Grill.** User picks findings → use the `grill-me` skill: one question at a time, a recommended answer each, resolve each branch before the next. No brainstorming, no question-picker UI. Walk each finding down its tree — confirm one concept, decide where it lives, what's deleted, which tests survive and which become dead. Concept gets a canonical name and `CONTEXT.md` exists → update it inline (`grill-with-docs` discipline).

The grill **terminates in conversation.** This skill does not implement, write a plan, or name what comes next — deciding what to remove and removing it are separate acts, and unslopping is not an HITL interview.
