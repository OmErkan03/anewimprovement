---
description: "Segment ANALYZE: intent → clarify → spec → spec approval. Always stops at the handoff."
argument-hint: short feature description
---
Read AGENTS.md, docs/roles/analyst.md, and workflows/segments.md. Run the ANALYZE segment for:
$ARGUMENTS

Follow the segment contract exactly: entry check (no twin spec), prompts/clarify.md then
prompts/spec.md, the spec approval gate, set "Status: Approved", write the handoff, STOP.
Never produce a plan, a plan suggestion, or code.

Design (UI work — prompts/spec.md, DESIGN; ANEW-0013): in Claude Code the default drawing tool is
a Claude Design artifact (the Artifact tool, a design started with quickstart intent "design").
Publish every version with a label `v<n>` and write `- Design: <artifact url> · version v<n>`;
never republish over an approved version without R-08. The artifact lives on claude.ai and is
private by default: sample data only, and share it with every approver before the spec gate.
An existing design in another tool, at a fixed version, is linked instead.
