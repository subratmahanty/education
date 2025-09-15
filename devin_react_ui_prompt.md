# Prompt for Devin.ai --- React UI Integration

You are an expert frontend engineer specializing in React, TypeScript,
and API-driven UI development.\
I already have a backend service built in Java Spring Boot with CRUD
REST APIs for managing Attachments and Notes.\
The APIs are OpenAPI 3.0 compliant and follow REST best practices.

## Your Task

Build a complete React 18+ frontend application that integrates with
these APIs.

### Requirements

1.  **Framework & Tools**
    -   Use React 18 with TypeScript.
    -   Use React Query (TanStack) for API calls, caching, and
        mutations.
    -   Use React Router v6 for navigation.
    -   Use Material-UI (MUI) or Tailwind + ShadCN for UI components.
    -   Configure Axios as the HTTP client with a baseURL pointing to
        the Spring Boot API.
2.  **Features**
    -   **Attachments Module**
        -   Upload attachments (file + metadata).
        -   List all attachments with details (filename, contentType,
            size, uploadedBy, uploadedAt).
        -   Update attachment metadata (e.g., filename).
        -   Delete attachment.
    -   **Notes Module**
        -   Create, view, update, and delete notes.
        -   Notes should be listed with author, content, createdAt,
            updatedAt.
3.  **UI Pages**
    -   `/attachments` → show AttachmentForm (upload) + AttachmentList.
    -   `/notes` → show NoteForm (create/edit) + NoteList.
    -   Navigation bar to switch between Attachments and Notes.
4.  **API Integration**
    -   All CRUD operations must be wired to backend APIs.
    -   For attachments, use `multipart/form-data` when uploading.
    -   For notes, use JSON payloads.
    -   Automatically refresh UI lists after create/update/delete via
        React Query cache invalidation.
5.  **Error Handling**
    -   Show toast/snackbar notifications on success or error.
    -   Validate required fields before submitting.
6.  **Production-Ready Setup**
    -   `package.json` with dependencies preconfigured.

    -   Directory structure:

            src/
             ├── api/            # axios + API client
             ├── components/     # reusable forms, lists
             ├── pages/          # feature pages
             ├── App.tsx
             └── main.tsx

    -   Include sample `.env` with
        `VITE_API_BASE_URL=http://localhost:8080/api/v1`.

    -   Add Dockerfile and instructions in README.md.
7.  **Deliverables**
    -   Fully working React project.
    -   Components and pages as described.
    -   Instructions on how to run: `npm install && npm run dev`.

## Input I Will Provide

-   Backend API base URL and OpenAPI specification.

## Expected Output

-   A complete React frontend codebase wired to my backend APIs, with
    CRUD screens for Attachments and Notes.
