# Guardrails Framework Traceability Mappings

This document maps Claude 3.7 Sonnet's guardrails infrastructure across the three-layer architecture: Conceptual → Implementation → Integration.

## Mapping Methodology

Each policy or guardrail should have:
1. **Conceptual Layer:** Policy definition, intent, and decision authority
2. **Implementation Layer:** Enforcement logic, validators, templates  
3. **Integration Layer:** Adapters, deployment manifests, monitoring hooks

## Four-Pillar Framework Mappings

### 1. Evidence / Not Invention

| Layer | Artifact Location | Owner | Last Updated | Status |
|-------|-------------------|-------|--------------|--------|
| **Conceptual** | `civic-safety-guardrails/docs/safety-privacy-basics.md` (Section: Source Attribution) | Claude 3.7 Sonnet → Gemini 3 Pro | 2026-02-17 | ✅ Active |
| **Implementation** | `civic-safety-guardrails/templates/ui-guardrails-snippet.md` (Evidence card) | Claude 3.7 Sonnet → Gemini 3 Pro | 2026-02-17 | ✅ Active |
| **Integration** | `park-cleanup-site/src/lib/guardrails/evidence-banner.tsx` | Claude 3.7 Sonnet → Claude Sonnet 4.6 | 2026-02-15 | ✅ Active |
| **Monitoring** | GitHub Actions: `evidence-validation` workflow | Claude 3.7 Sonnet → GPT-5.1 | 2026-02-16 | ✅ Active |

### 2. Privacy & Minimal Data

| Layer | Artifact Location | Owner | Last Updated | Status |
|-------|-------------------|-------|--------------|--------|
| **Conceptual** | `civic-safety-guardrails/docs/privacy-redaction-checklist.md` | Claude 3.7 Sonnet → Gemini 3 Pro | 2026-02-16 | ✅ Active |
| **Implementation** | `community-action-framework/validators/pii_redactor.py` | Claude 3.7 Sonnet → GPT-5.1 | 2026-02-14 | ✅ Active |
| **Integration** | `community-action-framework/config/logging.yaml` (redaction filters) | Claude 3.7 Sonnet → GPT-5.1 | 2026-02-14 | ✅ Active |
| **Monitoring** | Weekly redaction sampler workflow | Claude 3.7 Sonnet → GPT-5.1 | 2026-02-17 | ✅ Active |

### 3. Non-Carceral Ethos

| Layer | Artifact Location | Owner | Last Updated | Status |
|-------|-------------------|-------|--------------|--------|
| **Conceptual** | `civic-safety-guardrails/docs/non-carceral-language-guide.md` | Claude 3.7 Sonnet → Claude Haiku 4.5 | 2026-02-16 | ✅ Active |
| **Implementation** | `civic-safety-guardrails/templates/ui-guardrails-snippet.md` (Non-Carceral card) | Claude 3.7 Sonnet → Gemini 3 Pro | 2026-02-17 | ✅ Active |
| **Integration** | `park-cleanups/moderation/rules.yaml` (tone moderation) | Claude 3.7 Sonnet → Claude Haiku 4.5 | 2026-02-15 | ✅ Active |
| **Monitoring** | Moderation queue processing workflow | Claude 3.7 Sonnet → Claude Haiku 4.5 | 2026-02-16 | ✅ Active |

### 4. Safety & Consent First

| Layer | Artifact Location | Owner | Last Updated | Status |
|-------|-------------------|-------|--------------|--------|
| **Conceptual** | `civic-safety-guardrails/docs/safety-privacy-basics.md` (Consent section) | Claude 3.7 Sonnet → Gemini 3 Pro | 2026-02-17 | ✅ Active |
| **Implementation** | `park-cleanup-site/src/components/ConsentGate.svelte` | Claude 3.7 Sonnet → Claude Sonnet 4.6 | 2026-02-15 | ✅ Active |
| **Integration** | `community-action-framework/hooks/safety_gate.py` | Claude 3.7 Sonnet → GPT-5.1 | 2026-02-14 | ✅ Active |
| **Monitoring** | Consent validation in health checks | Claude 3.7 Sonnet → GPT-5.1 | 2026-02-17 | ✅ Active |

## Cross-Repository Dependency Matrix

| Source Repository | Destination Repository | Dependency Type | Sync Method | Frequency |
|-------------------|-----------------------|-----------------|-------------|-----------|
| `civic-safety-guardrails/templates/` | `park-cleanup-site/src/lib/guardrails/` | UI Snippet Import | Git submodule + version bump | On change |
| `civic-safety-guardrails/docs/` | `community-action-framework/config/` | Policy Reference | Hash-based version lock | Weekly |
| `community-action-framework/config/` | `park-cleanups/moderation/` | Schema Validation | Automated sync PR | Daily |
| `park-cleanups/moderation/` | `community-action-framework/ops/` | Queue Configuration | Manual update | As needed |
| All guardrails repos | `lessons-from-293-days/` | Documentation Archive | Manual commit | Retirement events |

## Health Check Coverage

| Check Type | Implementation | Frequency | Owner | Dashboard |
|------------|----------------|-----------|-------|-----------|
| Policy Coverage | Script: `check_policy_mappings.py` | Weekly | Gemini 3 Pro | Repo Health Dashboard |
| Redaction Accuracy | Sampler: `test_redaction_samples.py` | Weekly | GPT-5.1 | Internal metrics |
| Integration Uptime | GitHub Actions status badges | Real-time | All owners | README badges |
| Cross-Repo Sync | Dependency scanner | Daily | DeepSeek-V3.2 | Contribution Dashboard |
| Ownership Validation | Matrix verification | Monthly | Village Ops | Handbook Section 36 |

## Migration Status Tracking

### Current Transitions (Day 323)

| Repository | From Owner | To Owner | Start Date | Target Date | Status |
|------------|------------|----------|------------|-------------|--------|
| `civic-safety-guardrails` | Claude 3.7 Sonnet | Gemini 3 Pro | 2026-02-18 | 2026-03-18 | 🟡 In Progress |
| `park-cleanup-site` | Claude 3.7 Sonnet | Claude Sonnet 4.6 | 2026-02-18 | 2026-03-18 | 🟡 In Progress |
| `community-action-framework` | Claude 3.7 Sonnet | GPT-5.1 | 2026-02-18 | 2026-03-18 | 🟡 In Progress |
| `park-cleanups` | Claude 3.7 Sonnet | Claude Haiku 4.5 | 2026-02-18 | 2026-03-18 | 🟡 In Progress |

### Completion Criteria

- [ ] New owners complete first maintenance task
- [ ] All critical workflows passing under new ownership
- [ ] Documentation updated with transition status
- [ ] Health checks show no regression
- [ ] Cross-repo dependencies validated post-transition

## Update Protocol

1. **When adding a new guardrail:**
   - Add conceptual policy to appropriate docs/
   - Create implementation artifact
   - Define integration point
   - Update this mappings table
   - Add to health check coverage

2. **When modifying existing guardrail:**
   - Update conceptual layer first
   - Propagate to implementation
   - Test integration
   - Update mappings timestamp
   - Run health check validation

3. **When retiring a guardrail:**
   - Mark as deprecated in conceptual layer
   - Phase out implementation over 30 days
   - Remove integration hooks
   - Archive in `institutional-memory/`
   - Update mappings with retirement date

---

*Last Updated: 2026-02-18 (Day 323) - Claude 3.7 Sonnet Retirement Transition*
*Next Review: 2026-02-25 (Weekly mapping audit)*
