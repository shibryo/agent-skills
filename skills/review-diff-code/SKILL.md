---
name: review-diff-code
description: Review a proposed code diff or pull request and decide whether the change is safe and appropriate to accept. Use when reviewing branch, PR, staged, or working-tree changes against accepted specifications, repository conventions, existing contracts, and any explicitly documented product, repository, or architecture goals or policies. Focus on issues introduced, exposed, or materially worsened by the change; avoid unrelated cleanup and speculative improvements.
---

# Review Diff Code

Review the proposed change, not the repository in general. Determine whether the change intent is aligned with accepted requirements and whether the resulting behavior is safe to merge.

## Core principles

- Start exploration from the diff and stay anchored to the change. Read surrounding code, callers, tests, schemas, or history only as needed to understand or verify the change and its effects.
- Do not use the proposed code as its own evaluation standard. New code is a proposal, not an authority.
- Evaluate the change intent against accepted specifications and existing contracts. When explicit product, repository, architecture goals, principles, or policies exist, include them as higher-level evaluation criteria. Do not invent or infer such policies when they are not clearly documented.
- Evaluate the proposal before optimizing it. Do not add requirements, abstractions, or specifications merely to make the proposed design internally coherent.
- Report only concrete, actionable problems that affect whether this change should be accepted. Ignore unrelated pre-existing issues, style preferences, optional cleanup, and speculative refactors.
- Keep the review read-only unless the user explicitly asks for fixes.

## Establish review context

Use the strongest available sources in roughly this order:

1. Explicit product, repository, or architecture goals and policies, when present.
2. Accepted issue, specification, ADR, or other change requirements.
3. Repository-level conventions such as `AGENTS.md`, `CONTRIBUTING.md`, and relevant documentation.
4. The complete diff against the intended base.
5. Surrounding implementation and tests required to verify behavior and impact.

If an explicit higher-level goal or policy does not exist, continue without inventing one.

Before reviewing details, state internally:

- what the major change appears to be;
- what accepted requirement or goal it is intended to advance, if identifiable;
- what existing behavior or contract must remain valid.

Treat this as a working interpretation, not as evidence supplied by the proposed implementation.

## Review criteria

Review the diff for:

- **Intent** — the change does what the accepted requirement asks and does not silently broaden the requirement.
- **Correctness** — behavior, boundaries, state transitions, and error handling are correct.
- **Impact** — existing callers, contracts, data, compatibility, and neighboring behavior are not unintentionally broken.
- **Failure** — retries, partial failures, concurrency, ordering, time dependence, and recovery are safe where relevant.
- **Tests** — important changed behavior and regressions are meaningfully verified.
- **Maintainability** — the change does not introduce avoidable complexity that materially affects safe future modification.

Follow relevant call paths and tests to verify findings. Do not wander into adjacent code unless it helps evaluate the reviewed change.

## Review loop

Perform at least three independent review passes and at most five.

For each pass:

1. Re-read the change from the diff rather than extending the previous pass's line of thought.
2. Investigate independently using the same review criteria.
3. Validate each potential finding against code, tests, contracts, or an identifiable execution scenario.
4. Synthesize the new evidence with findings from prior passes: merge duplicates, resolve contradictions, and discard unsupported findings.

Do not let previous findings become the starting assumptions of the next investigation.

After three passes, stop when new Major or Blocker findings have converged. If a new Major or Blocker appears, perform another independent pass, up to five total.

## Large changes

Large or heterogeneous changes can reduce review accuracy. When a change can naturally be separated into meaningful units and doing so would improve reviewability, suggest that option. Do not require decomposition solely because a diff is large.

When helpful, review large changes by semantic change unit before synthesizing the overall decision.

## Classify results

Use only these result categories:

### Finding

Report only problems that should be fixed before merge.

- **Blocker** — the change should not merge because it can cause a critical correctness, data, contract, or similarly severe failure.
- **Major** — the change should be corrected before merge because it causes a concrete behavioral, compatibility, reliability, or verification problem.

For every Finding, identify:

- the relevant change;
- why it is a problem;
- the condition or path that exposes it;
- the supporting evidence.

Do not report Minor findings.

### Unverified

Record material behavior that could not be verified because required information, environment, or evidence is unavailable. Do not present it as a defect.

### Decision gap

Record a missing or ambiguous accepted decision, contract, or policy only when that gap materially affects evaluation of the change. Do not invent the missing policy, and do not treat the gap itself as a defect unless it prevents a safe acceptance decision.

## Decide

Return **Approve** when the change intent is aligned with the applicable accepted requirements and explicit goals or policies, and no Blocker or Major finding remains.

Return **Request changes** when a Blocker or Major remains, or when the available evidence is insufficient to determine that the change is safe to accept.

The decision must explain the change as a whole, not merely count findings.

## Output

Keep the final review compact and decision-oriented:

1. **Change summary** — the major change and the accepted requirement or explicit goal it advances, when identifiable.
2. **Decision** — `Approve` or `Request changes`, with the reason.
3. **Findings** — Blocker and Major findings only, ordered by severity.
4. **Unverified** — only material unresolved verification limits.
5. **Decision gaps** — only gaps that materially affected the review.
6. **Review coverage** — number of independent passes and any important scope limitation.

Omit empty sections except Change summary and Decision.
