# ANEW-0013 — A design is drawn at ANALYZE and approved with the spec

- Status: Accepted
- Date: 2026-11-12

## Context
ANEW-0008 put user experience into the spec: flow, states and accessibility in text, and a link
to a design "if there is one". A team that builds internal software with ANEW usually has no
designer and no design, so the link stays empty. The human approves a UI spec without seeing a
single screen, and the first real look at the screens comes at VERIFY, when changing them is
expensive. ANEW-0008 named this as a revisit trigger: "Teams need design approval as a separate
human gate before ANALYZE ends."

## Decision
For UI work, the Analyst draws the design at ANALYZE from the spec, or links an existing design at
a fixed version. The design covers every screen an acceptance criterion touches, and every listed
state that changes how a screen looks. The spec names it as `- Design: <link> · version <label>`.
The spec approval is one decision: the spec together with that design version. No new gate,
segment or role is added. A design change after approval is a spec change (R-08).
`scripts/doctor` checks the field's shape on `specs/active/`: WARN on a Draft, a gate (FAIL under
`--strict`) once the spec is Approved or In progress. A spec without a User experience heading
counts as UI. The core names no design tool (ANEW-0011). The Claude Code adapter uses a Claude
Design artifact as its default; other adapters link whatever tool their team uses.

## Consequences
- Benefits: the human sees the screens before approving; VERIFY compares against an approved,
  fixed version; a design that drifts after approval becomes a visible spec change.
- Costs: ANALYZE takes longer for UI work. The Analyst draws screens, which ANEW-0008 had
  forbidden ("never invent visuals"). The guard is that the design shows only what the spec says.
  doctor checks the shape only: it cannot open the link, confirm that the version exists, or
  judge whether the drawing matches the criteria. That stays with the human at the gate and with
  VERIFY. Designs hosted outside the repo (e.g. claude.ai) carry sample data only and must be
  shared with the approvers. Teams that upgrade with a UI spec already In progress get a red
  `--strict` until they add its design.

## Alternatives considered
- A separate design gate or DESIGN segment: rejected for the same reason as in ANEW-0008. It
  would add a stop without adding a decision, since the design is approved as part of the spec.
- Naming Claude Design in the core: rejected. It breaks ANEW-0011 and leaves teams on other tools
  with a rule they cannot follow.
- No doctor check (documented only, ANEW-0004): rejected. A gate rule that nothing checks is
  advice.

## Revisit triggers
- Design tools offer stable per-version links (tighten the field to require one).
- Approved designs repeatedly disagree with the shipped screens (consider a VERIFY-side check).
- Analyst-drawn designs add screens or states that the spec does not have.
