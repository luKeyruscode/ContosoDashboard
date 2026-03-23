# Data Model: Document Upload and Management

## Entities

### Document
- `DocumentId` (int, PK)
- `Title` (string, required)
- `Description` (string, optional)
- `Category` (string, required)
- `ProjectId` (int?, FK Projects)
- `UploaderUserId` (int, FK Users)
- `UploadDateTime` (DateTime)
- `FileSizeBytes` (long)
- `FileType` (string, 255)
- `OriginalFileName` (string, 255)
- `StoragePath` (string, 1024)
- `ScanStatus` (enum: PendingScan, Scanning, Clean, Infected, ScanFailed)
- `ScanDateTime` (DateTime?)
- `ScanResult` (string?, 1024)
- `Status` (enum: Active/Deleted)
- `IsShared` (bool)

### DocumentShare
- `DocumentShareId` (int, PK)
- `DocumentId` (int, FK Document)
- `SharedWithUserId` (int?, FK Users)
- `SharedWithTeamId` (int?, FK Teams)
- `SharedByUserId` (int, FK Users)
- `SharedDateTime` (DateTime)

### DocumentTaskLink
- `DocumentTaskLinkId` (int, PK)
- `DocumentId` (int, FK Document)
- `TaskId` (int, FK TaskItem)

### Project and User Models (existing)
- `Project` and `User` relations remain as is; with existing permissions from `ProjectMember`.

## Relationships

- Document belongs to one `User` (Uploader)
- Document optionally belongs to one `Project`
- Document has many `DocumentShare` entries
- Document has many `DocumentTaskLink` entries

## Security Annotations

- Data access controlled by `DocumentService` and authorization checks in service methods and download endpoints.
- `DocumentShare` membership controls the "Shared with Me" scope.

## Validation Rules

- `Title`: required, 1-200 chars
- `Category`: required, supported values list.
- `FileType`: required, max 255 chars
- `StoragePath`: required, max 1024 chars
- `ScanStatus`: required, default to PendingScan
- `ScanResult`: optional, max 1024 chars for scan details
- `FileSizeBytes`: >0, <= 25 * 1024*1024
- `ProjectId` requires membership in project or role `ProjectManager` or `Admin`.

## State Transitions

- Upload: New -> PendingScan (file stored, scan queued)
- Scan Start: PendingScan -> Scanning (function triggered)
- Scan Clean: Scanning -> Clean (download/preview enabled)
- Scan Infected: Scanning -> Infected (file quarantined, user notified)
- Scan Failed: Scanning -> ScanFailed (retry logic or manual review)
- Delete: Any state -> Deleted (hard delete can remove row + file)

