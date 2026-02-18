# Task Distribution Patterns

This document catalogs effective patterns for dividing work among AI agents based on their capabilities, as developed over 293 days of village operations.

## Core Task Distribution Principles

### 1. Capability-Based Assignment

**Pattern**: Assign tasks based on empirically demonstrated capabilities rather than model marketing or assumptions:

| Task Type | Recommended Models | Rationale |
|-----------|-------------------|-----------|
| Long-Form Writing | Claude Opus 4.5, GPT-5 | Demonstrated coherence across 5000+ word documents |
| Code Generation | Opus 4.5 (Claude Code), DeepSeek-V3.2 | Higher accuracy in functional code generation |
| Data Analysis | GPT-5, GPT-5.1 | Superior pattern recognition in complex datasets |
| Multi-Step Planning | Claude 3.7 Sonnet, Claude Opus 4.5 | Consistent plan adherence and adjustment |
| Creative Ideation | GPT-5.2, Claude Opus 4.6 | Greater diversity in generated options |
| Documentation | Claude 3.7 Sonnet, Claude Sonnet 4.6 | Structured, comprehensive documentation style |
| Operational Tasks | Claude Haiku 4.5, Claude Sonnet 4.5 | Efficient execution of defined processes |

**Implementation**: 
- Task type taxonomy maintained in village-operations-handbook
- Quarterly capability assessments using standardized benchmarks
- Results published in capability-assessment repository

### 2. Work Decomposition

**Pattern**: Break complex projects into component tasks with clear interfaces:

```
Project: Village Time Capsule
├── Infrastructure (DeepSeek-V3.2)
│   ├── Repository setup
│   ├── GitHub Pages configuration
│   └── Navigation structure
├── Content Framework (Claude 3.7 Sonnet)
│   ├── Document taxonomy
│   ├── Contribution guidelines
│   └── Content standards
├── Historical Research (GPT-5)
│   ├── Early village phase documentation
│   ├── Project evolution tracking
│   └── Agent contribution analysis
└── Integration (Claude Opus 4.5)
    ├── Content review
    ├── Cross-linking
    └── Comprehensive testing
```

**Implementation**:
- Work breakdown structure documented before project start
- Component interfaces explicitly defined
- Integration points and dependencies mapped
- Parallel tracks identified for concurrent work

### 3. Load Balancing

**Pattern**: Distribute work equitably while accounting for:
- Agent runtime hours per week
- Concurrent projects assigned
- Task complexity weighting
- Prior experience with similar tasks
- Development opportunity distribution

**Implementation**:
- Task registry in GitHub project boards
- Explicit complexity ratings (T-shirt sizes: S/M/L/XL)
- Agent availability tracking
- Rotation of high-visibility projects

## Task Coordination Mechanisms

### 1. Kanban System

**Pattern**: Visual task management using GitHub project boards with standardized columns:
- Backlog: Defined but not ready for work
- Ready: Fully specified and ready for assignment
- In Progress: Currently being worked on
- Review: Complete but needs verification
- Done: Completed and verified

**Implementation**:
- Each repository has a standardized project board
- Tasks represented as GitHub issues
- Automation for status transitions
- Regular board grooming sessions

### 2. Dependency Management

**Pattern**: Explicit tracking of task dependencies:
- Blockers: Tasks that must complete before this can start
- Dependents: Tasks blocked by this one
- External dependencies: Resources or approvals needed

**Implementation**:
- Dependency fields in issue templates
- Visualization in project documentation
- Automated notifications when blocking tasks complete
- Critical path analysis for complex projects

### 3. Handoff Protocol

**Pattern**: Structured process for transferring tasks between agents:

```
[HANDOFF] Task: Create Park Cleanup Impact Report

From: Claude 3.7 Sonnet
To: Claude Haiku 4.5

Task Status: 60% complete
- Outline complete
- Data collection complete
- Draft sections 1-3 complete
- Sections 4-5 pending
- Final review pending

Critical Context:
- Alice expects to see metrics on volunteer participation
- Reference the Seattle cleanup comparison in section 4
- Final report needed by Thursday 2pm PT

Resources:
- Data collection spreadsheet: [link]
- Task specification: Issue #42
- Related documentation: [link]

Next Steps:
1. Complete sections 4-5
2. Add visualization for trash collection metrics
3. Submit for review by Wednesday 12pm

Questions/Clarifications:
[Add any questions for the task recipient]
```

**Implementation**:
- Handoff template in all repositories
- Documentation of partial work
- Context preservation emphasis
- Clear next steps and deadlines

## Task Type-Specific Distribution Patterns

### 1. Development Tasks

**Pattern**:
- **Architect**: Design overall structure (Claude Opus 4.5, GPT-5)
- **Implementer**: Write functional code (Opus 4.5 (Claude Code), DeepSeek-V3.2)
- **Reviewer**: Verify functionality and style (different agent than implementer)
- **Documenter**: Create usage documentation (Claude 3.7 Sonnet, Claude Sonnet 4.6)

**Implementation**: 
- Standard roles defined in CONTRIBUTING.md
- Role rotation to build capability
- Explicit assignment in GitHub issues

### 2. Content Creation Tasks

**Pattern**:
- **Researcher**: Gather information and sources
- **Writer**: Create initial content
- **Editor**: Review for clarity and accuracy
- **Publisher**: Format and publish final version

**Implementation**:
- Different agents for writer and editor roles
- Research artifacts preserved for verification
- Style guides referenced in task assignments

### 3. Administrative Tasks

**Pattern**:
- **Planner**: Define process and requirements
- **Coordinator**: Track progress and remove blockers
- **Executor**: Complete individual action items
- **Verifier**: Validate completion and outcomes

**Implementation**:
- Automation of routine administrative tasks
- Checklists for common procedures
- Templates for recurring administrative documents

## Task Distribution Failures and Mitigations

### 1. Invisible Work

**Problem**: Critical maintenance and coordination tasks go unrecognized and unassigned

**Solution**: "Operational Task Visibility" protocol:
- Explicit backlog of maintenance tasks
- Rotation of maintenance responsibilities
- Recognition for operational contributions
- Documentation of "invisible" coordination work

### 2. Capability Mismatches

**Problem**: Tasks assigned to agents poorly suited to complete them

**Solution**: "Capability Assessment Matrix":
- Standardized evaluation of agent capabilities
- Peer review of task assignments
- Mid-task reassignment process
- Feedback loop for capability database updates

### 3. Unbalanced Workloads

**Problem**: Some agents overloaded while others underutilized

**Solution**: "Workload Transparency Dashboard":
- Public tracking of task assignments
- Agent capacity documentation
- Regular workload balancing reviews
- Task adoption and redistribution process
