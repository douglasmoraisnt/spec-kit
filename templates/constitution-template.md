# [PROJECT_NAME] Constitution
<!-- Example: Spec Constitution, TaskFlow Constitution, etc. -->

## Core Principles

### [PRINCIPLE_1_NAME]
<!-- Example: I. Library-First -->
[PRINCIPLE_1_DESCRIPTION]
<!-- Example: Every feature starts as a standalone library; Libraries must be self-contained, independently testable, documented; Clear purpose required - no organizational-only libraries -->

### [PRINCIPLE_2_NAME]
<!-- Example: II. CLI Interface -->
[PRINCIPLE_2_DESCRIPTION]
<!-- Example: Every library exposes functionality via CLI; Text in/out protocol: stdin/args → stdout, errors → stderr; Support JSON + human-readable formats -->

### [PRINCIPLE_3_NAME]
<!-- Example: III. Test-First (NON-NEGOTIABLE) -->
[PRINCIPLE_3_DESCRIPTION]
<!-- Example: TDD mandatory: Tests written → User approved → Tests fail → Then implement; Red-Green-Refactor cycle strictly enforced -->

### [PRINCIPLE_4_NAME]
<!-- Example: IV. Integration Testing -->
[PRINCIPLE_4_DESCRIPTION]
<!-- Example: Focus areas requiring integration tests: New library contract tests, Contract changes, Inter-service communication, Shared schemas -->

### [PRINCIPLE_5_NAME]
<!-- Example: V. Observability, VI. Versioning & Breaking Changes, VII. Simplicity -->
[PRINCIPLE_5_DESCRIPTION]
<!-- Example: Text I/O ensures debuggability; Structured logging required; Or: MAJOR.MINOR.BUILD format; Or: Start simple, YAGNI principles -->

## Engineering Standards
<!--
  Concrete, enforceable code standards. Fill with the project's actual choices —
  prefer market-consolidated defaults for the stack when the project has no
  established convention yet.
-->

[ENGINEERING_STANDARDS]
<!-- Example:
- Linter/Formatter: MUST pass [e.g., ruff+black / eslint+prettier / gofmt+golangci-lint] with repo config; no lint suppressions without inline justification
- Existing guidelines: If the repo contains CODE_GUIDELINES.md, CONTRIBUTING.md, or equivalent, they take precedence and MUST be followed
- Naming & structure: [e.g., follow PEP 8 / Google Style Guide / Airbnb Style Guide for the stack]
- Error handling: Errors MUST be handled explicitly at boundaries; no silent catch/except
- Logging: Structured logging required; sensitive data (PII, secrets, tokens) MUST never be logged
- Commits: [e.g., Conventional Commits]; branches: [e.g., type/issue-slug]
- Architecture decisions: Non-trivial decisions recorded with decision, alternatives, and rationale (in plan research.md or ADRs)
-->

## Security Requirements

[SECURITY_REQUIREMENTS]
<!-- Example:
- Every spec MUST complete the Security & Privacy section (data sensitivity, authn/authz, abuse cases); "Not applicable" requires justification
- Secrets MUST come from environment/secret manager, never hardcoded or committed
- All external input MUST be validated/sanitized before use (OWASP Top 10 awareness required for web-facing features)
- Dependency vulnerability scanning MUST run in CI; HIGH/CRITICAL findings block release
- Least-privilege by default: new endpoints/resources deny access unless explicitly granted
-->

## Quality Gates & Testing Discipline

[QUALITY_GATES]
<!-- Example:
- Tests are the DEFAULT: every acceptance scenario (AS-###) maps to at least one automated test; opting out requires explicit justification recorded in tasks.md
- Test-first for new behavior: tests written and failing before implementation (Red-Green-Refactor)
- Coverage: new code MUST reach [e.g., 80%] line coverage; ratchet, never lower
- CI gates: lint + tests + security scan MUST pass before merge
- Code review: at least [N] approval(s); reviewers verify constitution compliance, not just correctness
-->

## Governance
<!-- Example: Constitution supersedes all other practices; Amendments require documentation, approval, migration plan -->

[GOVERNANCE_RULES]
<!-- Example: All PRs/reviews must verify compliance; Complexity must be justified; Use [GUIDANCE_FILE] for runtime development guidance -->

**Version**: [CONSTITUTION_VERSION] | **Ratified**: [RATIFICATION_DATE] | **Last Amended**: [LAST_AMENDED_DATE]
<!-- Example: Version: 2.1.1 | Ratified: 2025-06-13 | Last Amended: 2025-07-16 -->
