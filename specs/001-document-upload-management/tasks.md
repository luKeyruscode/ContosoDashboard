# Tasks: Document Upload and Management

**Input**: Design documents from `/specs/001-document-upload-management/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: The examples below include test tasks. Tests are OPTIONAL - only include them if explicitly requested in the feature specification.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Single project**: `src/`, `tests/` at repository root
- **Web app**: `backend/src/`, `frontend/src/`
- **Mobile**: `api/src/`, `ios/src/` or `android/src/`
- Paths shown below assume single project - adjust based on plan.md structure

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure

- [ ] T001 Create Azure Functions project structure for DocumentScanner in DocumentScanner/DocumentScanner.csproj
- [ ] T002 Add Azure Functions dependencies to DocumentScanner.csproj (Azure.Storage.Queues, Azure.Storage.Blobs, Microsoft.Azure.Functions.Worker)
- [ ] T003 Configure Azure Functions host.json and local.settings.json for queue triggers
- [ ] T004 Add document management dependencies to ContosoDashboard.csproj (Azure.Storage.Queues, Azure.Storage.Blobs)
- [ ] T005 [P] Configure appsettings.json for document storage and Azure services
- [ ] T006 [P] Create secure upload directory structure outside wwwroot

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T007 Create database migration for Document, DocumentShare, and DocumentTaskLink entities
- [ ] T008 [P] Implement IFileStorageService interface in Services/IFileStorageService.cs
- [ ] T009 [P] Implement LocalFileStorageService in Services/LocalFileStorageService.cs
- [ ] T010 [P] Implement IQueueService interface in Services/IQueueService.cs
- [ ] T011 [P] Implement AzureQueueService in Services/AzureQueueService.cs
- [ ] T012 [P] Create DocumentService base class in Services/DocumentService.cs with authorization checks
- [ ] T013 [P] Register new services in Program.cs (DocumentService, IFileStorageService, IQueueService)
- [ ] T014 Create base API controller structure for document endpoints

**Checkpoint**: Foundation ready - user story implementation can now begin in parallel

---

## Phase 3: User Story 1 - Upload and annotate documents (Priority: P1) 🎯 MVP

**Goal**: Enable users to upload files with metadata and project linkage, storing them securely with virus scan queuing

**Independent Test**: Create a document upload session with required metadata and verify file appears in user documents and metadata is persisted with PendingScan status

### Implementation for User Story 1

- [ ] T015 [P] [US1] Create Document model in Models/Document.cs with all required fields including ScanStatus
- [ ] T016 [P] [US1] Create DocumentShare model in Models/DocumentShare.cs
- [ ] T017 [P] [US1] Create DocumentTaskLink model in Models/DocumentTaskLink.cs
- [ ] T018 [US1] Implement upload validation in DocumentService (file type, size, metadata)
- [ ] T019 [US1] Implement file storage logic in DocumentService using IFileStorageService
- [ ] T020 [US1] Implement queue message creation for virus scanning in DocumentService
- [ ] T021 [US1] Create POST /api/documents endpoint in Controllers/DocumentsController.cs
- [ ] T022 [US1] Add multipart form handling and file processing to upload endpoint
- [ ] T023 [US1] Implement upload progress indication in Documents.razor page
- [ ] T024 [US1] Add error handling for upload failures (file type, size, network issues)

**Checkpoint**: At this point, User Story 1 should be fully functional and testable independently

---

## Phase 4: User Story 2 - Manage and find documents (Priority: P1)

**Goal**: Enable browsing, filtering, searching, and sorting of accessible documents

**Independent Test**: After multiple uploaded documents, validate sorting, category filtering, project scope filtering, and keyword search returns correct subset

### Implementation for User Story 2

- [ ] T025 [US2] Implement GET /api/documents endpoint with filtering and search in Controllers/DocumentsController.cs
- [ ] T026 [US2] Add category, project, date range, and search query filtering to DocumentService
- [ ] T027 [US2] Implement sorting by title, upload date, and file size in DocumentService
- [ ] T028 [US2] Create Documents.razor page for document listing and filtering UI
- [ ] T029 [US2] Add project documents view in ProjectDocuments.razor page
- [ ] T030 [US2] Implement role-based access control for document visibility (owner, shared, project member)
- [ ] T031 [US2] Add pagination support for large document lists
- [ ] T032 [US2] Optimize search performance for 500+ documents

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently

---

## Phase 5: User Story 3 - Secure access, preview, and lifecycle actions (Priority: P2)

**Goal**: Enable secure download, preview, metadata editing, and document deletion with proper authorization

**Independent Test**: Upload a document, edit metadata, replace file content, then delete and verify it is removed from listings and storage

### Implementation for User Story 3

- [ ] T033 [US3] Implement GET /api/documents/{id}/download endpoint with authorization checks
- [ ] T034 [US3] Add scan status verification before allowing downloads
- [ ] T035 [US3] Implement GET /api/documents/{id}/preview endpoint for PDF and images
- [ ] T036 [US3] Create DocumentDetails.razor page for individual document view and actions
- [ ] T037 [US3] Implement PUT /api/documents/{id} for metadata updates (title, category, tags)
- [ ] T038 [US3] Implement PUT /api/documents/{id}/replace for file content replacement
- [ ] T039 [US3] Implement DELETE /api/documents/{id} with owner/project manager authorization
- [ ] T040 [US3] Add audit logging for all lifecycle operations (download, edit, delete)
- [ ] T041 [US3] Implement file cleanup on deletion (remove from storage)

**Checkpoint**: User Stories 1, 2, and 3 should now be independently functional

---

## Phase 6: User Story 4 - Sharing and notifications (Priority: P2)

**Goal**: Enable document sharing with users/teams and notification delivery

**Independent Test**: Share a document, confirm recipient gets in-app notification and document appears in "Shared with Me" for recipient

### Implementation for User Story 4

- [ ] T042 [US4] Implement POST /api/documents/{id}/share endpoint for sharing with users/teams
- [ ] T043 [US4] Add sharing logic to DocumentService with DocumentShare entity management
- [ ] T044 [US4] Implement GET /api/documents/shared-with-me endpoint
- [ ] T045 [US4] Extend NotificationService to handle document sharing notifications
- [ ] T046 [US4] Create "Shared with Me" view in Documents.razor
- [ ] T047 [US4] Add notification display for document shares
- [ ] T048 [US4] Implement access control for shared documents in service layer

**Checkpoint**: User Stories 1-4 should now be independently functional

---

## Phase 7: User Story 5 - Task and dashboard integration (Priority: P3)

**Goal**: Enable document attachment to tasks and display recent documents on dashboard

**Independent Test**: Attach document from task details, check task scope association and dashboard widget updates

### Implementation for User Story 5

- [ ] T049 [US5] Implement document attachment logic in task pages (TaskDetails.razor)
- [ ] T050 [US5] Add DocumentTaskLink management to DocumentService
- [ ] T051 [US5] Create dashboard widget for recent documents in Index.razor
- [ ] T052 [US5] Implement document count summary in dashboard
- [ ] T053 [US5] Add task document integration to Tasks.razor page
- [ ] T054 [US5] Update project document views to include task attachments

**Checkpoint**: All user stories should now be independently functional

---

## Phase 8: Background Processing - Virus Scanning

**Purpose**: Implement Azure Functions background processing for virus scanning

- [ ] T055 Create DocumentScanFunction.cs in DocumentScanner project with queue trigger
- [ ] T056 Implement virus scanning logic using Windows Defender or third-party service
- [ ] T057 Add scan result processing and status updates to DocumentScanFunction
- [ ] T058 Implement scan failure retry logic with exponential backoff
- [ ] T059 Add scan result notifications to users
- [ ] T060 Configure Azure Functions deployment settings

---

## Phase 9: Polish & Cross-Cutting Concerns

**Purpose**: Improvements that affect multiple user stories

- [ ] T061 [P] Add comprehensive error handling and user-friendly messages across all endpoints
- [ ] T062 [P] Implement audit logging for all document operations
- [ ] T063 [P] Add input validation and sanitization for all user inputs
- [ ] T064 [P] Performance optimization for document listing and search operations
- [ ] T065 [P] Security hardening - implement rate limiting and additional authorization checks
- [ ] T066 [P] Update documentation and help text in UI
- [ ] T067 [P] Run quickstart.md validation and update if needed
- [ ] T068 [P] Add accessibility improvements to document management UI

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3-7)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Background Processing (Phase 8)**: Depends on User Story 1 completion
- **Polish (Phase 9)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No dependencies on other stories
- **User Story 2 (P1)**: Can start after Foundational (Phase 2) - Independent of US1
- **User Story 3 (P2)**: Can start after Foundational (Phase 2) - Independent of US1/US2
- **User Story 4 (P2)**: Can start after Foundational (Phase 2) - Independent of US1/US2/US3
- **User Story 5 (P3)**: Can start after Foundational (Phase 2) - Independent of other stories

### Within Each User Story

- Models before services
- Services before endpoints
- Core implementation before integration
- Story complete before moving to next priority

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel (within Phase 2)
- Once Foundational phase completes, all user stories can start in parallel (if team capacity allows)
- Models within a story marked [P] can run in parallel
- Different user stories can be worked on in parallel by different team members

---

## Parallel Example: User Story 1

```bash
# Launch all models for User Story 1 together:
Task: "Create Document model in Models/Document.cs with all required fields including ScanStatus"
Task: "Create DocumentShare model in Models/DocumentShare.cs"
Task: "Create DocumentTaskLink model in Models/DocumentTaskLink.cs"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Test User Story 1 independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Test independently → Deploy/Demo
4. Add User Story 3 → Test independently → Deploy/Demo
5. Add User Story 4 → Test independently → Deploy/Demo
6. Add User Story 5 → Test independently → Deploy/Demo
7. Add Background Processing → Deploy/Demo
8. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1 (Upload)
   - Developer B: User Story 2 (Browse/Search)
   - Developer C: User Story 3 (Lifecycle)
   - Developer D: User Story 4 (Sharing)
   - Developer E: User Story 5 (Integration)
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and testable
- Verify tests fail before implementing
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently</content>
<parameter name="filePath">/home/luana-ramos/Documents/course github spec/ContosoDashboard/specs/001-document-upload-management/tasks.md