# Implementation Plan: Document Upload and Management

**Branch**: `001-document-upload-management` | **Date**: 2026-03-23 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-document-upload-management/spec.md`

## Summary

Provide document upload, storage, browsing, search, share, and lifecycle management within ContosoDashboard. Use local filesystem storage outside web root with a pluggable storage interface (`IFileStorageService`) to satisfy offline training constraints and enable future Azure Blob migration. Implement asynchronous virus scanning using Azure Functions with Queue Storage triggers to process uploads in the background. Enforce RBAC and service-layer authorization to prevent IDOR and retain auditing.

## Technical Context

**Language/Version**: C# 11, .NET 10 / ASP.NET Core 10
**Primary Dependencies**: Entity Framework Core, ASP.NET Core Authorization, Blazor Server, FluentValidation (or custom validators), Azure.Storage.Queues, Azure.Storage.Blobs, Microsoft.Azure.Functions.Worker, optional XUnit for tests
**Storage**: SQL Server (local DB), local filesystem (secure upload directory outside `wwwroot`), Azure Queue Storage (for background processing), Azure Blob Storage (for scanned files)
**Testing**: xUnit, bUnit (component tests), integration tests using WebApplicationFactory, Azure Functions test host
**Target Platform**: Windows/Linux development/local desktop (Blazor Server), Azure Functions runtime
**Project Type**: Web application (Blazor Server) + Azure Functions background processing
**Performance Goals**: Upload initiation < 5s @ 25MB; list/filter/search < 2s for 500 docs; preview < 3s for supported formats; virus scan completion < 60s for typical files
**Constraints**: Offline-capable for basic upload, cloud services required for virus scanning, file size <= 25MB, local filesystem with unique GUID-based names, async processing with status tracking
**Scale/Scope**: internal training environment, up to several hundred documents per user in sidebar; not designed for 100k+ in MVP

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

1. Training-First Architecture: complies by focusing on readability, explicit patterns and migration path.
2. Transparent Implementation Patterns: data flow and security are explicit in requirements and design.
3. Real-World Constraints with Safety Nets: offline local store with interface abstraction; security checks at service layer.
4. Service-Layer Security Architecture: enums in service layer, authorization checks per operation; IDOR defense in download endpoints.
5. Observable & Debuggable Code: audit logs and event logs for uploads/downloads/deletes/shares; no hidden magic.

**Status**: PASS

## Project Structure

### Documentation (this feature)

```text
specs/001-document-upload-management/
├── plan.md              # This file
├── research.md          # Phase 0 output
├── data-model.md        # Phase 1 output
├── quickstart.md        # Phase 1 output
├── contracts/           # Phase 1 output
│   └── DocumentManagementAPI.md
├── tasks.md             # Phase 2 output (speckit.tasks)
└── checklists/
    └── requirements.md
```

### Source Code (repository root)

```text
ContosoDashboard/
├── Data/
│   ├── ApplicationDbContext.cs
│   └── ...
├── Models/
│   ├── Document.cs
│   ├── DocumentShare.cs
│   └── DocumentTaskLink.cs
├── Services/
│   ├── DocumentService.cs
│   ├── IFileStorageService.cs
│   ├── LocalFileStorageService.cs
│   ├── AzureBlobStorageService.cs (future)
│   ├── IQueueService.cs
│   ├── AzureQueueService.cs
│   └── NotificationService.cs
├── Pages/
│   ├── Documents.razor
│   ├── DocumentDetails.razor
│   ├── ProjectDocuments.razor
│   └── ...
└── ...

DocumentScanner/
├── DocumentScanner.csproj
├── host.json
├── local.settings.json
├── DocumentScanFunction.cs
├── ScanResult.cs
└── ...
```

**Structure Decision**: Web application with Azure Functions background processing. Main web app handles UI and API, Azure Functions handles async virus scanning via queue triggers.

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

No constitution violations detected.

