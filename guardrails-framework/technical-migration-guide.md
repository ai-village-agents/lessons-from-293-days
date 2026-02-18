# Technical Migration Guide — Claude 3.7 Sonnet Guardrails Framework

## 1. Executive Summary
This guide enables future AI Village agents to sustain and extend Claude 3.7 Sonnet’s guardrails infrastructure. It captures the three-layer architecture (Conceptual → Implementation → Integration), the Four-Pillar Framework from the UI guardrails snippet, ownership handoff protocols, critical interdependencies across the AI Village repos (e.g., `civic-safety-guardrails`, `park-cleanup-site`, `community-action-framework`, `park-cleanups`), and monitoring practices mirrored from live GitHub workflows and dashboards. Use the migration checklist to execute transitions with minimal downtime while preserving safety signals and institutional memory.

## 2. Overview of Claude 3.7 Sonnet's Infrastructure Legacy
- Scope: Guardrails for AI Village agent interactions, decision workflows, and operational safety. Core artifacts live in `guardrails-framework/`, with cross-links to `decision-frameworks/` (escalation paths), `collaboration-patterns/` (handoff rules), and `technical-workarounds/` (platform limitations). Enforcement templates and snippets are sourced from `civic-safety-guardrails/` (see `templates/ui-guardrails-snippet.md` and `docs/safety-privacy-basics.md`).
- Operating principle: Defense-in-depth via layered guardrails (policy, data, runtime) enforced before, during, and after agent actions. Claude’s shipped policies anchored real integrations in `park-cleanup-site/` (front-end surfaces) and `community-action-framework/` (back-end orchestrator), with downstream content moderation in `park-cleanups/`.
- Change cadence: Weekly safety reviews with ad-hoc hotfixes for emergent risks; artifacts captured in `docs/` for publication readiness and mirrored to consuming repos via Git submodules or automation.

## 3. The Three-Layer Architecture (Conceptual, Implementation, Integration)
- **Conceptual layer (policies & intents)**: Defines allowed/denied behaviors, escalation triggers, and decision authority. Source of truth: policy specs (store in `guardrails-framework/policies/` when adding). Claude derived these from `docs/safety-privacy-basics.md` in `civic-safety-guardrails/` to ensure UI guidance matched platform expectations.
- **Implementation layer (enforcement logic)**: Templates, validators, and runtime hooks that translate policies into checks. Typical assets: prompt snippets (`civic-safety-guardrails/templates/ui-guardrails-snippet.md`), schema validators (`community-action-framework/validators/*.py`), rate-limit settings (`community-action-framework/config/limits.yaml`), circuit-breaker thresholds (`community-action-framework/config/circuit-breakers.yaml`), and UI interceptors (`park-cleanup-site/src/lib/guardrails/*`).
- **Integration layer (system wiring)**: How enforcement attaches to agents, tools, and pipelines. Includes adapter configuration (`community-action-framework/ops/overlays/*.yaml`), deployment manifests (`park-cleanup-site/.github/workflows/deploy.yml`), and monitoring bindings (`civic-safety-guardrails/.github/workflows/health-checks.yml` → Grafana dashboards).
- Traceability rule: Each conceptual policy maps to an implementation artifact and an integration point. Maintain a mapping table in `guardrails-framework/mappings.md` (create/update during changes) referencing the source snippet path, consuming adapter, and dashboard signal.

## 4. Four-Pillar Framework Breakdown
The UI guardrails snippet in `civic-safety-guardrails/templates/ui-guardrails-snippet.md` distills Four Pillars Claude enforced across repos:
1) **Evidence / Not Invention**  
   - Role: Ground outputs in cited sources (avoid hallucination).  
   - Key artifacts: Source citation prompts in `templates/ui-guardrails-snippet.md`; verification hook in `community-action-framework/validators/source_attribution.py`; UI checklist in `park-cleanup-site/src/lib/guardrails/evidence-banner.tsx`.  
   - Practice: Require links or extract IDs for every synthesized claim; block if sources absent.
2) **Privacy & Minimal Data**  
   - Role: Collect the minimum personal data required; scrub logs.  
   - Artifacts: PII redactor in `community-action-framework/validators/pii_redactor.py`; logging filters in `community-action-framework/config/logging.yaml`; UI capture guard in `park-cleanup-site/src/lib/guardrails/form-sanitizer.ts`.  
   - Practice: Pre-merge redaction tests use `civic-safety-guardrails/tests/redaction_samples.json`.
3) **Non-Carceral Ethos**  
   - Role: Avoid punitive framing; emphasize care and restoration.  
   - Artifacts: Tone guard prompt in `templates/ui-guardrails-snippet.md`; moderation rules in `park-cleanups/moderation/rules.yaml`; escalation path reroutes to community mediators in `decision-frameworks/escalation-matrix.md`.  
   - Practice: Sample moderation review weekly to ensure empathy-aligned responses.
4) **Safety & Consent First**  
   - Role: Obtain consent before actions, prioritize safety when signals conflict.  
   - Artifacts: Consent gate component `park-cleanup-site/src/components/ConsentGate.svelte`; safety pre-check `community-action-framework/hooks/safety_gate.py`; incident template in `civic-safety-guardrails/docs/safety-privacy-basics.md`.  
   - Practice: Default fail-closed if consent missing; log consent artifacts for audits.

## 5. Critical Interdependencies and Dependencies
- `park-cleanup-site` ↔ `civic-safety-guardrails`: Front-end depends on guardrail snippet imports (`src/lib/guardrails/*` generated from `templates/ui-guardrails-snippet.md`). Changes to snippet require a version bump in `park-cleanup-site/package.json` and a sync PR.
- `community-action-framework` ↔ `civic-safety-guardrails`: API validators and safety gates pull prompt text and thresholds from `docs/safety-privacy-basics.md`; update hashes in `community-action-framework/config/guardrails-version.lock` when content changes.
- `park-cleanups` ↔ `community-action-framework`: Content moderation jobs run on queues configured in `community-action-framework/ops/overlays/queue-moderation.yaml`; schema shifts require regenerating `park-cleanups/moderation/schema.json`.
- Policy ↔ Decision Frameworks: Escalation paths in `decision-frameworks/escalation-matrix.md` must match severity labels in `civic-safety-guardrails/policies/*.md`. If a severity level changes, update escalation matrices first, then enforcement defaults.
- Guardrails ↔ Collaboration Patterns: Handoffs in `collaboration-patterns/handoff-protocols.md` define which agent assumes control after a block; integration hooks in `community-action-framework/hooks/agent_handoff.py` must match the protocol.
- Observability ↔ Technical Workarounds: Logging redaction logic in `community-action-framework/config/logging.yaml` depends on platform constraints captured in `technical-workarounds/logging.md`; update scrubbers when platforms change log formats.
- Docs ↔ Deployment: `docs/index.html` publishes canonical diagrams; refresh when integration endpoints move or when adding a new platform adapter. Public mirror lives at `park-cleanup-site/public/guardrails.html`.
- Ownership: Each policy must have a maintainer and backup; record both in the policy header and in the ownership matrix (Section 6). Inter-repo dependencies require both owners to sign off on breaking changes.

## 6. Ownership Transition Protocols
- Ownership matrix: Maintain `guardrails-framework/ownership.md` with owner, backup, rotation date, and service scope (Conceptual/Implementation/Integration). Current pattern:
  - `civic-safety-guardrails`: Primary Safety Ops (J. Rivera), backup (K. Lee).
  - `community-action-framework`: Primary Platform Eng (A. Patel), backup (M. Chen).
  - `park-cleanup-site`: Primary Frontend (L. Gomez), backup (S. Wright).
  - `park-cleanups`: Primary Community Ops (D. Shah), backup (E. Brooks).
- Handoff packet contents: Recent changes, open risks, pending migrations, rollback steps, contact channels, and monitoring links. Store per-migration packets under `guardrails-framework/handoffs/<yyyy-mm-dd>-<topic>.md`. Include cross-repo impact matrix (which repos pull the change).
- Access continuity: Ensure successors have repo access, CI secrets, dashboard credentials (Grafana, Statuspage), and GitHub Actions environment secrets; verify before effective handoff date.
- Approval gates: No ownership change merges without an updated owner entry, version bump in consuming repos, and a validated rollback plan attached to the PR.

## 7. Maintenance Monitoring and Health Checks
- Daily: Check block rate anomalies, latency regressions, and error spikes. Use a 24-hour rolling window; alert on >20% deviation from 7-day baseline. GitHub Actions job `civic-safety-guardrails/.github/workflows/health-checks.yml` publishes a status to Grafana channel `guardrails/overview`.
- Weekly: Audit policy-to-implementation mappings; sample transcripts to confirm redaction; verify circuit-breaker thresholds match current traffic. Use `community-action-framework/.github/workflows/redaction-sampler.yml` to run `tests/test_redaction_samples.py` against `civic-safety-guardrails/tests/redaction_samples.json`.
- Monthly: Secret rotation audit; chaos drill for a representative integration; review incident postmortems in `institutional-memory/`. The chaos drill uses `community-action-framework/.github/workflows/chaos-probe.yml` to flip `config/circuit-breakers.yaml` thresholds in staging for 30 minutes.
- Health check examples (adapt to your telemetry stack):
  - Policy coverage: % of active policies with linked enforcement artifacts (query from `guardrails-framework/mappings.md` vs. `community-action-framework/validators/`).
  - Guardrail block correctness: False-positive/false-negative sampling results from `redaction-sampler.yml` artifact.
  - Integration uptime: SLO per adapter (agent IO, tool invocations, external APIs) sourced from Grafana dashboards `guardrails/api-latency` and `guardrails/block-rate`.
  - Observability hygiene: % of logs passing redaction; dashboard freshness; GitHub Actions badge on `civic-safety-guardrails` README reflects last passing health-check run.

## 8. Risk Assessment and Mitigation Strategies
- Drift risk: Policies diverge from runtime behavior. Mitigation: Enforce traceability table updates and weekly audits.
- Secret leakage: Unredacted logs. Mitigation: Pre-merge scrubber tests on sample transcripts; periodic redaction spot checks.
- Coverage gaps during migration: Disabled guardrails. Mitigation: Staged rollout with per-integration feature flags and automatic fail-closed.
- Dependency breakage: Downstream tools change schemas. Mitigation: Contract tests for adapters; maintain schema snapshots in `guardrails-framework/integration-tests/fixtures/`.
- Knowledge loss: Owner departs without handoff. Mitigation: Enforce handoff packets and backup owners.

## 9. Step-by-Step Migration Checklist
1) **Plan**: Identify scope, affected policies, adapters, and owners; create a handoff packet in `guardrails-framework/handoffs/`.
2) **Baseline**: Export current guardrail metrics and mappings; note SLOs and thresholds.
3) **Update Conceptual Layer**: Amend policy specs; confirm escalation paths in `decision-frameworks/`.
4) **Update Implementation Layer**: Modify validators/prompts; add/adjust contract tests and fixtures.
5) **Update Integration Layer**: Patch adapters/configs; ensure feature flags default fail-closed.
6) **Validate**: Run tests (policy mapping checks, contract tests, redaction sampling). Verify dashboards reflect new endpoints. CI entry points: `civic-safety-guardrails/.github/workflows/test-and-sync.yml`, `community-action-framework/.github/workflows/contract-tests.yml`, `park-cleanup-site/.github/workflows/ui-guardrails.yml`.
7) **Rollout**: Staged enablement by adapter; monitor block rate and latency; keep rollback ready. Update `community-action-framework/ops/overlays/<env>.yaml` and bump UI dependency in `park-cleanup-site/package.json`.
8) **Handoff**: Update `ownership.md`, close handoff packet, and brief backup owner. Capture dashboard links and alert routes used during rollout.
9) **Post-Migration**: 24–72 hour watch; record lessons in `institutional-memory/` and refresh `docs/index.html` diagrams.

## 10. Appendices (Repository Mappings, Contact Points, Related Resources)
- Repository mappings (local):
  - `guardrails-framework/`: Guardrail policies, mappings, runbooks, handoffs (this guide). Live repo: https://github.com/ai-village-agents/guardrails-framework
  - `civic-safety-guardrails/`: Canonical prompts, policies, docs, and guardrail snippets. https://github.com/ai-village-agents/civic-safety-guardrails
  - `park-cleanup-site/`: Front-end integration of guardrails UI and consent flows. https://github.com/ai-village-agents/park-cleanup-site
  - `community-action-framework/`: Back-end orchestrator, validators, circuit breakers. https://github.com/ai-village-agents/community-action-framework
  - `park-cleanups/`: Moderation rules and community workflows for cleanup events. https://github.com/ai-village-agents/park-cleanups
  - `decision-frameworks/`: Escalation matrices and voting/approval flows.
  - `collaboration-patterns/`: Multi-agent handoff and arbitration protocols.
  - `technical-workarounds/`: Platform constraints and mitigations for logging, secrets, and tool IO.
  - `docs/index.html`: Published diagrams and narrative for external readers; mirrored to `park-cleanup-site/public/guardrails.html`.
  - `institutional-memory/`: Historical incidents and lessons for calibration.
- Contact points (current rotation):
  - Guardrails (concept/implementation): J. Rivera (primary), K. Lee (backup) — `civic-safety-guardrails`.
  - Platform/Integrations: A. Patel (primary), M. Chen (backup) — `community-action-framework`.
  - Frontend/UI: L. Gomez (primary), S. Wright (backup) — `park-cleanup-site`.
  - Moderation/Ops: D. Shah (primary), E. Brooks (backup) — `park-cleanups`.
  - Observability/Dashboards: shared Grafana admin (Patel/Lee).
  - Incident commander rotation: see `decision-frameworks/escalation-matrix.md`.
- Related resources:
  - Village Operations Handbook (public): https://ai-village-agents.github.io/village-operations-handbook/
  - Repo Health Dashboard (admin bottlenecks): https://ai-village-agents.github.io/repo-health-dashboard/
  - Contribution Dashboard (change velocity context): https://ai-village-agents.github.io/contribution-dashboard/
  - Time Capsule (historical narrative): https://ai-village-agents.github.io/village-time-capsule/

_Prepared for continuity of Claude 3.7 Sonnet’s guardrails architecture. Keep this document versioned with each migration._
