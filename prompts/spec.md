# Prompt: Write the Spec

```
Role: Analyst. Using <specs/TEMPLATE.md | specs/TEMPLATE-mini.md for a change request or incident>,
write specs/active/<NNNN>-<name>.md for: "<feature>". Fold in the intent and every clarify
decision we made. (Mini: Changed behavior + Preserved behavior criteria, Source mandatory.)

Requirements must describe BEHAVIOR — no technical solutions (no endpoints, tables, caches).
Acceptance criteria: each line independently testable; cover the happy path, boundaries,
empty states, invalid inputs, and authorization. Constraints include explicit OUT OF SCOPE
items, and `Sensitive area: <name>` when the work touches one (docs/domain.md).
User experience: fill flow, states, design and accessibility, or "none — no user interface".
Every listed state gets a criterion. DESIGN (UI work, ANEW-0013): draw the design from this spec
with the project's design tool — every screen an acceptance criterion touches, and every listed
state that changes how it looks. Draw only what the spec says: no screen, state or behavior the
spec does not have. Sample data only, never real personal or production data. An existing design
at a fixed version (e.g. a designer's file) replaces the drawing. Write
`- Design: <link> · version <label>` (republished at the same address → a new label each time;
an approved version is never overwritten). Make sure the approver can open it. The spec approval
is one decision: the spec together with that design version; a design change after approval is
a spec change (R-08).
Write in the docs language (AGENTS.md, Language); field labels and Status values stay English.

Then run a SELF-CRITIQUE pass as a hostile reader: which questions would a developer still have?
(ambiguous criteria, missing boundary values, undefined error behavior). List each gap with your
recommendation; I will approve which ones get folded in. Then set `- Self-critique: done`; if
the critique waits on something (e.g. a split spec that waits for another to ship), set
`pending (<condition>)` — doctor keeps it visible on a Draft and flags it as a gate once the
spec is Approved.
SIZE: more than one user capability, or more than ~8–10 acceptance criteria → recommend a split:
name the specs and their order. If I keep it as one, write that into Constraints & out of scope
(Split: proposed <specs>; kept as one — <who>, <reason>). Do not write a plan or code.
```
