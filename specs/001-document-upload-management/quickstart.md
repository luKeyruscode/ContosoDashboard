# Quickstart: Document Upload and Management

## What this feature enables

- Upload files with metadata and categorization
- Secure local storage outside web root
- Browse/filters/search in own documents and project documents
- Preview common file types (PDF, images) and download with authorization
- Share documents with users/teams, notify recipients
- Attach docs to tasks and show recent docs on dashboard

## Setup

1. Ensure local storage directory is configured in `appsettings.Development.json`:

```json
"DocumentStorage": {
  "BasePath": "./AppData/uploads"
}
```

2. Configure Azure services in `appsettings.Development.json`:

```json
"Azure": {
  "QueueStorage": {
    "ConnectionString": "UseDevelopmentStorage=true"
  },
  "BlobStorage": {
    "ConnectionString": "UseDevelopmentStorage=true",
    "ContainerName": "scanned-documents"
  }
}
```

3. Ensure migrations exist and apply DB schema changes for new entities:

```bash
dotnet ef migrations add AddDocumentManagement
dotnet ef database update
```

4. Register services in `Program.cs`:

```csharp
builder.Services.AddScoped<IFileStorageService, LocalFileStorageService>();
builder.Services.AddScoped<IQueueService, AzureQueueService>();
builder.Services.AddScoped<DocumentService>();
```

5. Set up authorization policies in `Program.cs`:

```csharp
builder.Services.AddAuthorization(options => {
  options.AddPolicy("DocumentOwnerOrProjectManager", policy =>
      policy.RequireAssertion(context => {
          // implement claim and membership checks
      }));
});
```

6. Deploy Azure Functions locally for development:

```bash
cd DocumentScanner
func start
```

## Key interactions

- Navigate to `/documents` to view My Documents (shows scan status)
- Click "Upload" to select files and provide metadata (immediate response, background scan)
- Documents show scan status: Pending, Scanning, Clean, Infected
- Only Clean documents are downloadable/previewable
- Infected documents show quarantine notice with admin contact

## Tests to run

- Upload a valid file and verify DB record + file path
- Reject unsupported file types and oversized payloads
- Authorized download returns file, unauthorized returns 403
- Share flow: recipient gets notification + shared list entry
- Search flow respects permissions and returns results <2s

