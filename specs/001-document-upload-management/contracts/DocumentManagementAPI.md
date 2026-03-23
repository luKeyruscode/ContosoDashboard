# API Contract: Document Management

## Endpoints

### GET /api/documents
- Description: List documents accessible to current user (owned, shared, project member).
- Query parameters:
  - `category` (optional)
  - `projectId` (optional)
  - `search` (optional: title/description/tags/uploader)
  - `fromDate`/`toDate` (optional)
  - `sortBy` (optional: title, uploadDate, fileSize)
  - `sortOrder` (optional: asc/desc)
- Response: 200
  - `documents[]` with metadata fields (DocumentId, Title, Category, ProjectId, UploadedBy, UploadDateTime, FileSizeBytes, FileType, ScanStatus, ScanDateTime, IsShared)

### POST /api/documents
- Description: Upload one or more documents.
- Request: multipart/form-data
  - `files[]` (required)
  - `title` (required)
  - `description` (optional)
  - `category` (required)
  - `projectId` (optional)
  - `tags[]` (optional)
- Response: 201
  - `uploaded[]` with DocumentId, status, and scanStatus (initially "PendingScan")
- Errors: 400 for unsupported file type/size, 401 unauthorized, 403 forbidden.

### GET /api/documents/{id}
- Description: Get document metadata and access controls.
- Response: 200 with full metadata; 404 if no access.

### GET /api/documents/{id}/scan-status
- Description: Get current virus scan status for a document.
- Response: 200 with scanStatus, scanDateTime, scanResult (if available).

### GET /api/documents/{id}/download
- Description: Download document binary after authorization check and scan verification.
- Response: 200 content stream with headers `Content-Disposition`, `Content-Type`.
- Errors: 403 unauthorized or scan not clean, 404 not found.

### GET /api/documents/{id}/preview
- Description: Return inline compatible stream if type is previewable (PDF/Image).
- Response: 200 partial content for preview.

### PUT /api/documents/{id}
- Description: Update metadata for owned documents.
- Request: JSON body (title, description, category, tags array).
- Response: 200 updated metadata.
- Errors: 403 if not owner.

### PUT /api/documents/{id}/replace
- Description: Replace the stored file content with new upload and update related metadata fields.
- Request: multipart/form-data with `file` and optional metadata updates.
- Response: 200 updated metadata, new FilePath.

### DELETE /api/documents/{id}
- Description: Soft/hard delete document for owner/project manager.
- Response: 204 on success.

### POST /api/documents/{id}/share
- Description: Share document with users or teams.
- Request: JSON { sharedWithUserIds: [], sharedWithTeamIds: [] }
- Response: 200 share details + notification status.

### GET /api/documents/shared-with-me
- Description: List documents shared to user.
- Response: 200 list.

## Security

- Authorization one of:
  - owner
  - project manager in document project
  - shared recipient
  - administrator
- Download/preview endpoints enforce runtime check using service: `DocumentService.CanAccessDocument(userId, documentId)`.
- All endpoints must use anti-forgery middleware for state-changing operations.

## Error Contracts

- 400: invalid input with `errors[]` field for validation messages.
- 401: unauthorized (not logged in).
- 403: forbidden (not permitted to access resource).
- 404: document not found.
- 500: unexpected errors; body includes `errorId` and safe message.

