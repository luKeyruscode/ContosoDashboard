<!-- 
=================================================================
SYNC IMPACT REPORT – Constitution v1.0.0
=================================================================

**Version Change**: Initial creation (v1.0.0)
**Bump Rationale**: First formal constitution for ContosoDashboard training project

**Modified Principles**: None (initial version)
**Added Sections**: 
  - I. Training-First Architecture
  - II. Transparent Implementation Patterns
  - III. Real-World Constraints with Safety Nets
  - IV. Service-Layer Security Architecture
  - V. Observable & Debuggable Code
  - Governance section with amendment procedure

**Removed Sections**: None

**Templates Status**:
  ✅ .specify/templates/plan-template.md – No changes needed
    (Already includes "Constitution Check" gate that will use principles)
  ✅ .specify/templates/spec-template.md – No changes needed
    (Supports user story prioritization aligned with transparent implementation)
  ✅ .specify/templates/tasks-template.md – No changes needed
    (Organizes tasks by story for independent delivery aligned with principle II)
  ✅ README.md – No changes needed
    (Existing "Architecture Principles" already reflect training focus)

**Files Flagged for Manual Review**: None

**Deferred TODOs**: None

=================================================================
-->

# ContosoDashboard Constitution

## Core Principles

### I. Training-First Architecture

Every design decision prioritizes learning value for developers studying Spec-Driven Development (SDD) and clean architecture patterns. Code MUST be intentionally simple, well-documented, and serve as a reference implementation. Complexity is justified ONLY when it demonstrates a specific architectural pattern. Production hardening is explicitly NOT required; clarity and educational value supersede performance optimization.

### II. Transparent Implementation Patterns

All features demonstrate clear patterns that trainees can study and replicate. Data flow, service organization, security implementation, and state management MUST be observable and traceable. Comments explain the "why" behind architectural choices. No hidden utilities or magic—common patterns are explicit and discoverable.

### III. Real-World Constraints with Safety Nets

Implementation follows ASP.NET Core and Blazor best practices while acknowledging training limitations. Mock systems (authentication, data persistence) simulate production patterns safely. Service-level authorization prevents IDOR vulnerabilities. Role-based access control demonstrates proper gating. When production patterns cannot be fully implemented, the limitation is documented with guidance for production migration.

### IV. Service-Layer Security Architecture

Security implementation MUST occur at the service layer (business logic), never relying solely on page-level authorization. Every service method validates user claims and enforces role-based access control independently. Cross-cutting concerns like IDOR protection are explicit in code. Tests verify that direct service calls (bypassing UI) still enforce proper authorization. This pattern demonstrates defense-in-depth security design.

### V. Observable & Debuggable Code

Code flow MUST be traceable without external instrumentation or debugging tools. Structured logging of state transitions, user actions, and decision points is required. Data relationships are explicit and visible through clear model definitions and referential integrity. Comments explain non-obvious business logic. Console output and logs provide sufficient information for trainees to understand "what happened and why" without stepping through a debugger.

## Governance

**Amendment Procedure**: Constitution amendments require:
1. Proposed change documented with rationale
2. Assessment of impact on existing features and templates
3. Version bump according to semantic rules (MAJOR=principle redefinition, MINOR=new principle, PATCH=wording clarification)
4. Update to `.specify/` templates to maintain consistency
5. Documentation of migration path for affected features

**Compliance Verification**: All code reviews MUST verify that pull requests align with stated principles. Deviations require explicit justification and exception documentation.

**Version Semantics**: MAJOR.MINOR.PATCH format.
- MAJOR: Principle removed or fundamentally redefined
- MINOR: New principle added or existing principle expanded
- PATCH: Wording clarifications or non-semantic refinements

**Version**: 1.0.0 | **Ratified**: 2026-03-23 | **Last Amended**: 2026-03-23
