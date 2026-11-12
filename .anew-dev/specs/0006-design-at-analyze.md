# Spec 0006 — A design is drawn and approved with the spec

- Status: In progress
- Mode: lite (ANEW's own development)
- Plan: `.anew-dev/plans/0006-plan.md`
- Self-critique: done (G-1, G-2, G-3 folded in below)

## Intent
The team uses ANEW to build internal software, and most of that software has screens. Today the
spec's "User experience" section lists flows and states in text and links a design only if one
exists. In practice there is usually none, so the human approves a spec without seeing the screens.
The first real look at a screen comes at VERIFY, when it is expensive to change.

With this change, ANALYZE produces a drawn design for every screen that an acceptance criterion
touches. The spec links that design at a fixed version, and the human approves both together as
one decision. VERIFY then compares the built screens against that approved version.

Success means a UI spec cannot be approved without a design at a fixed version. A later design
change is a visible spec change, and `doctor` catches a UI spec that has no design.

Deliberately not done: judging visual quality automatically, generating code from the design, a
Designer role, a separate design segment or gate, and syncing with any design tool.

## Requirements
1. **A design is required for UI work.** A spec whose "User experience" section is not
   `none — no user interface` carries a `Design:` field in the form
   `Design: <link> · version <label>`. The design covers every screen that an acceptance criterion
   touches. It also covers every state listed for that screen (empty, loading, error, success, no
   permission) that changes how the screen looks.
2. **The Analyst draws it in ANALYZE**, from the analysis, with the project's design tool. Every
   drawn screen and state is one the spec names; the design adds no screen, state or behavior that
   the spec does not have. The design uses sample data, never real personal or production data.
   An existing external design at a fixed version (for example a designer's file) satisfies the
   rule instead of a new drawing.
3. **The core names no tool** (ANEW-0011). In the Claude Code adapter, the default drawing tool is
   a Claude Design artifact (claude.ai). The other adapters link whatever design tool their team
   uses, and the commands stay at parity.
4. **One approval.** The spec approval gate is one decision: "approve spec NNNN together with
   design version <label>". `Status: Approved` covers both. No new segment, gate or role is added.
   Before the gate, the approver must be able to open the design.
5. **A fixed version.** The approved spec names one design version. Changing the design after
   approval is a spec change (R-08): a new version, a new `Design:` line and a new approval.
   A design that is republished at the same address gets a label on every publish (`v1`, `v2`, …),
   and the spec cites that label. A version that has been approved is never overwritten without
   a spec change (G-1).
6. **Mini-specs too.** In the CHANGE and bug-fix lanes, the mini-spec's `Design:` field is either
   `none — no user interface` or a link at a fixed version. The design covers the screens that the
   change touches. Trivial changes under `docs/git.md` "Trivial changes" are exempt.
7. **doctor enforces the field** on specs in `specs/active/`. A missing or malformed `Design:`
   field on a UI spec warns on a Draft (exit 0). On an `Approved` or `In progress` spec it warns,
   and it fails under `--strict`. `doctor` checks only the shape of the field, link plus version
   label. It does not open the link. Shipped specs are not judged. A spec with no "User
   experience" heading is treated as a UI spec, so it fails closed (G-3).
8. **VERIFY compares against the approved version.** UI evidence is compared with the design
   version named in the spec (`docs/testing.md`, Evidence for UI criteria).
9. **Recorded as a decision.** A new ADR, ANEW-0013, records this decision and revises ANEW-0008,
   whose revisit trigger ("design approval before ANALYZE ends") has now fired. ANEW-0008 gets a
   `Revised:` line that points to ANEW-0013.

## User experience
none — no user interface. (ANEW is Markdown plus POSIX scripts. This spec changes the Analyst's
work, not a screen.)

## Constraints & out of scope
- Tool-neutral core: no core file (`docs/`, `workflows/`, `prompts/`, `specs/` templates,
  `scripts/`) names a design tool. The Claude Design default lives only in
  `adapters/claude-code/`.
- Privacy: designs live outside the repo (ANEW-0006). A Claude Design artifact is hosted on
  claude.ai and is private by default. It holds sample data only, and it is shared with the
  approvers before the gate.
- `doctor` cannot verify that a version exists or that the drawing matches the criteria. That
  stays with the human at the gate and with VERIFY (as ANEW-0008 states).
- Release: this adds a new rule, so it ships on `main` as v1.25.0. It is not backported to
  `lts/1.24`, which takes fixes only.
- Upgrade (G-2): the rule is not relaxed for work already in flight. The v1.25.0 CHANGELOG entry
  carries an upgrade note: finish UI specs that are `In progress` before upgrading, or add their
  design (R-08). Otherwise `doctor --strict` turns CI red.
- Permalinks (G-1): PLAN checks whether a Claude Design artifact offers a stable link to one
  version. If it does, the Claude Code adapter tells the Analyst to use it. If it does not, the
  label rule in Req. 5 applies.
- Out of scope: B-1…B-7, any change to `check.yml`, and specs already shipped.

## Acceptance criteria
- [ ] AC-1 — `specs/TEMPLATE.md` "User experience" defines the `Design: <link> · version <label>`
  field and its scope (Req. 1). `specs/TEMPLATE-mini.md` `Design:` allows exactly that form or
  `none — no user interface` (Req. 6).
- [ ] AC-2 — `prompts/spec.md`, `prompts/clarify.md` and `docs/roles/analyst.md` tell the Analyst
  to draw the design from the analysis. The text covers every screen an AC touches and every state
  that changes its look, sample data only, nothing beyond the spec, and an external fixed-version
  design accepted instead (Req. 2). "Never invent visuals" is replaced by this rule.
- [ ] AC-3 — The spec approval gate in `workflows/segments.md` (ANALYZE row),
  `workflows/feature-development.md` step 2 and `workflows/change-request.md` MINI-SPEC asks one
  decision that names both the spec and the design version. `prompts/recovery/spec-change.md`
  (R-08) names a design change after approval (Req. 4, 5).
- [ ] AC-4 — selftest: a Draft UI spec with no `Design:` field, or a malformed one, makes
  `doctor` WARN and exit 0, in both normal and `--strict` mode. The same holds for a Draft spec
  with no "User experience" heading (Req. 7, G-3).
- [ ] AC-5 — selftest: the same spec at `Approved` or `In progress` makes `doctor` WARN with exit
  0 in normal mode, and FAIL with exit 1 under `--strict` (Req. 7).
- [ ] AC-6 — selftest: no Design warning for a spec whose User experience is
  `none — no user interface`, a valid `Design:` line, a mini-spec with `Design: none — no user
  interface` or a valid link, or any spec in `specs/done/` (Req. 1, 6, 7).
- [ ] AC-7 — `adapters/claude-code/` (the analyze and new-feature commands) names a Claude Design
  artifact as the default and states the privacy constraint (Req. 3). `grep -rn "Claude Design"`
  finds no core file outside `adapters/` and `docs/decisions/`. `doctor` adapter parity is ok.
- [ ] AC-8 — `docs/decisions/anew/ANEW-0013-*.md` exists and is Accepted. ANEW-0008 carries a
  `Revised:` line pointing to it. `docs/testing.md` compares UI evidence with the spec's design
  version (Req. 8). `docs/commands.md` describes the new gate wording, and doctor's command
  reference check is ok (Req. 9).
- [ ] AC-9 — `CHANGELOG.md` has a v1.25.0 entry with the upgrade note (G-2), and `.anew-version`
  reads v1.25.0.
  `./scripts/doctor --strict` exits 0 and `./scripts/selftest` is green.

## Definition of Done
- [ ] Every acceptance criterion mapped to proof (test or reproducible observation)
- [ ] `./scripts/doctor --strict` and `./scripts/selftest` green (this repo's check, `.anew-dev/README.md`)
- [ ] Independent review done; real findings fixed, noise rejected with written rationale, deferred ones in `.anew-dev/backlog.md`
- [ ] Docs / ADRs updated (ANEW-0013, ANEW-0008 revised)
- [ ] Spec moved to `.anew-dev/done/` (it becomes immutable there)

## Scorecard (fill at ship — honest numbers make the process improvable)
| Metric | Value |
|---|---|
| Spec revisions | |
| Fix rounds | |
| Review findings: real / noise / deferred | |
| Regressions introduced | |
| Bugs escaped to production | |
