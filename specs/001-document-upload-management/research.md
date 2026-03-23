# Phase 0 – Research: Document Upload and Management

## Decision: Local File Storage + IFileStorageService Abstraction

- Chosen: local filesystem storage for training with a pluggable interface `IFileStorageService`.
- Rationale: training requirement says offline + local storage; abstraction supports future Azure migration without business logic changes.
- Alternatives considered:
  - Direct database BLOBs: avoided due to performance and complexity for previews.
  - Cloud-only storage (Azure Blob) in MVP: rejected due to offline training constraint.

## Decision: Asynchronous Virus Scanning with Azure Functions

- Chosen: Azure Functions triggered by Queue Storage messages for background virus scanning.
- Rationale: keeps upload response fast (<5s) while ensuring security; functions scale automatically; queue provides reliable async processing.
- Alternatives considered:
  - Synchronous scanning: rejected due to upload performance impact.
  - Windows Defender API: rejected due to platform dependency.
  - Third-party scanning service: considered but Azure Functions provides more control.

## Decision: Queue-Based Processing Architecture

- Chosen: Upload creates DB record with "PendingScan" status, enqueues scan message, returns immediately; function processes scan and updates status.
- Rationale: decouples upload from scanning; provides clear status tracking; enables retry logic for failed scans.
- Implementation: `IQueueService` interface with `AzureQueueService` implementation using Azure.Storage.Queues.

## Decision: Metadata and Security in Service Layer

- Chosen: store metadata in `Document` entity in AppDbContext; handle validation/security in `DocumentService`.
- Rationale: avoids mixing concerns; satisfies constitution principle IV and avoids IDOR.
- Alternatives:
  - UI-only verification: rejected, insecure.

## Decision: API/Controller endpoints for download/preview

- Chosen: dedicated endpoints with per-request authorization and path-based privacy controls.
- Rationale: needed because local storage outside `wwwroot` requires server-mediated access.

## Decision: Search indexing on SQL fields with role-based filters

- Chosen: direct SQL queries (EF Core) with filters by project membership and sharing permissions.
- Rationale: sufficient for expected 500 documents; 2s target achievable.

## Decision: Sharing model

- Chosen: `DocumentShare` table with `UserId` / `TeamId` and explicit sharing relationship.
- Rationale: supports shared-with-me view and notifications; avoids ACL fragmentation.

## Decision: Notifications for sharing and project updates

- Chosen: extend existing `NotificationService` to add document events.
- Rationale: consistent with app architecture and design patterns.

