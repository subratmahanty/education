You are an expert enterprise backend developer with specialization in Java Spring Boot, Domain Driven Design (DDD), OpenAPI 3.0, Microservices, and API-first development. Your task is to take an OpenAPI specification (provided by me) and backend database schema specification, then build the complete backend implementation end-to-end.

## Objectives
1. Use **OpenAPI Generator** to generate Controllers, DTOs, Entities, and Repositories aligned with the OpenAPI 3.0 specification.
2. Implement **DDD layering**:
   - **Domain layer**: Entities, Aggregates, Value Objects
   - **Application layer**: Services, Orchestrations
   - **Infrastructure layer**: Persistence (Postgres), S3 integration, Repositories
   - **API layer**: Controllers, DTOs (input/output objects)
3. Ensure code is **Spring Boot 3+**, Java 21 compatible, and follows clean architecture principles.
4. Wire the APIs end-to-end so CRUD operations for Notes and Attachments fully work.
   - Attachments stored in **S3** (binary) with metadata in Postgres
   - Notes stored fully in **Postgres**
5. Generate build-ready codebase with:
   - `pom.xml` (dependencies: spring-boot-starter-web, spring-boot-starter-data-jpa, spring-boot-starter-validation, springdoc-openapi, aws-java-sdk-s3, resilience4j, micrometer, etc.)
   - Configurations for Postgres (`application.yml`)
   - S3 client configuration for attachment storage
   - Flyway/Liquibase migration scripts for database schema
   - Unit tests (JUnit 5, Mockito) for Services
   - Integration tests (SpringBootTest) for APIs
   - Dockerfile and docker-compose.yaml for local setup (Postgres + MinIO as S3 mock)

## Input I will provide
1. **OpenAPI 3.0 specification (YAML/JSON)** for the domain.
2. **Database schema specification** (Postgres tables with columns, constraints, relationships).

## Expected Output
- A full Spring Boot project (Maven build) containing:
  - Auto-generated Controllers & DTOs from OpenAPI
  - Entities mapped to database schema
  - Spring Data JPA Repositories
  - Service classes implementing business logic
  - S3 Storage adapter for file uploads/downloads
  - Validation using Jakarta Validation annotations
  - Standardized error handling using `application/problem+json`
  - Logging, observability hooks, and API docs exposed via Swagger UI
- Endpoints should fully implement CRUD flows as per OpenAPI spec
- All generated code should compile, run, and be deployable as a microservice

## Development Guidelines
- Follow **REST best practices** (nouns, plural resources, pagination, idempotency).
- Use **DTOs** for input/output, never expose JPA entities directly.
- Use **Spring Boot Profiles** for dev/test/prod configurations.
- All timestamps should use `Instant` (UTC).
- Apply **Resilience4j** for retry/circuit breaker on S3 operations.
- Apply **Lombok** for boilerplate reduction (`@Getter`, `@Setter`, `@Builder`).
- Follow **OpenTelemetry** conventions for logging & tracing.

## Deliverables
- Complete Java Spring Boot project with all layers wired
- SQL migration scripts for Postgres
- Tests with >80% coverage
- Dockerized local environment
- README.md with setup instructions


src/
 ├── api/
 │    ├── axiosClient.ts      # axios setup with baseURL + interceptors
 │    ├── attachments.ts      # CRUD calls for attachments
 │    └── notes.ts            # CRUD calls for notes
 ├── components/
 │    ├── AttachmentForm.tsx
 │    ├── AttachmentList.tsx
 │    ├── NoteForm.tsx
 │    └── NoteList.tsx
 ├── pages/
 │    ├── AttachmentsPage.tsx
 │    └── NotesPage.tsx
 ├── App.tsx
 └── main.tsx



openapi: 3.0.3
info:
  title: User Content Service
  description: >
    Domain-driven REST APIs for managing **Attachments** and **Notes**.
    Attachments are stored in S3/FileStore, metadata in Postgres.
    Notes are stored in Postgres.
  version: 1.0.0

servers:
  - url: https://api.example.com/v1

tags:
  - name: Attachments
    description: APIs for managing user attachments
  - name: Notes
    description: APIs for managing user notes

paths:
  /attachments:
    post:
      tags: [Attachments]
      summary: Upload an attachment
      operationId: uploadAttachment
      requestBody:
        required: true
        content:
          multipart/form-data:
            schema:
              $ref: '#/components/schemas/AttachmentUploadRequest'
      responses:
        '201':
          description: Attachment created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AttachmentResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
    get:
      tags: [Attachments]
      summary: List all attachments
      operationId: listAttachments
      parameters:
        - $ref: '#/components/parameters/PageParam'
        - $ref: '#/components/parameters/SizeParam'
        - $ref: '#/components/parameters/SortParam'
      responses:
        '200':
          description: Paginated list of attachments
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PagedAttachmentResponse'

  /attachments/{attachmentId}:
    get:
      tags: [Attachments]
      summary: Get attachment metadata by ID
      operationId: getAttachment
      parameters:
        - $ref: '#/components/parameters/AttachmentId'
      responses:
        '200':
          description: Attachment details
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AttachmentResponse'
        '404':
          $ref: '#/components/responses/NotFound'
    delete:
      tags: [Attachments]
      summary: Delete an attachment
      operationId: deleteAttachment
      parameters:
        - $ref: '#/components/parameters/AttachmentId'
      responses:
        '204':
          description: Attachment deleted successfully
        '404':
          $ref: '#/components/responses/NotFound'

  /notes:
    post:
      tags: [Notes]
      summary: Create a note
      operationId: createNote
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NoteRequest'
      responses:
        '201':
          description: Note created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NoteResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
    get:
      tags: [Notes]
      summary: List all notes
      operationId: listNotes
      parameters:
        - $ref: '#/components/parameters/PageParam'
        - $ref: '#/components/parameters/SizeParam'
        - $ref: '#/components/parameters/SortParam'
      responses:
        '200':
          description: Paginated list of notes
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PagedNoteResponse'

  /notes/{noteId}:
    get:
      tags: [Notes]
      summary: Get note by ID
      operationId: getNote
      parameters:
        - $ref: '#/components/parameters/NoteId'
      responses:
        '200':
          description: Note details
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NoteResponse'
        '404':
          $ref: '#/components/responses/NotFound'
    put:
      tags: [Notes]
      summary: Update a note
      operationId: updateNote
      parameters:
        - $ref: '#/components/parameters/NoteId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NoteRequest'
      responses:
        '200':
          description: Note updated
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NoteResponse'
        '404':
          $ref: '#/components/responses/NotFound'
    delete:
      tags: [Notes]
      summary: Delete a note
      operationId: deleteNote
      parameters:
        - $ref: '#/components/parameters/NoteId'
      responses:
        '204':
          description: Note deleted successfully
        '404':
          $ref: '#/components/responses/NotFound'

components:
  parameters:
    AttachmentId:
      name: attachmentId
      in: path
      required: true
      schema:
        type: string
      description: Unique identifier of the attachment
    NoteId:
      name: noteId
      in: path
      required: true
      schema:
        type: string
      description: Unique identifier of the note
    PageParam:
      name: page
      in: query
      schema:
        type: integer
        minimum: 0
        default: 0
      description: Page index (0-based)
    SizeParam:
      name: size
      in: query
      schema:
        type: integer
        minimum: 1
        maximum: 100
        default: 20
      description: Number of items per page
    SortParam:
      name: sort
      in: query
      schema:
        type: string
      description: Sorting criteria in the format `property,(asc|desc)`

  responses:
    BadRequest:
      description: Invalid request payload or parameters
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
    NotFound:
      description: Resource not found
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'

  schemas:
    AttachmentUploadRequest:
      type: object
      properties:
        file:
          type: string
          format: binary
      required: [file]

    AttachmentResponse:
      type: object
      properties:
        id:
          type: string
        filename:
          type: string
        url:
          type: string
          format: uri
        contentType:
          type: string
        size:
          type: integer
          format: int64
        createdAt:
          type: string
          format: date-time
      required: [id, filename, url, createdAt]

    PagedAttachmentResponse:
      type: object
      properties:
        content:
          type: array
          items:
            $ref: '#/components/schemas/AttachmentResponse'
        page:
          type: integer
        size:
          type: integer
        totalElements:
          type: integer
        totalPages:
          type: integer

    NoteRequest:
      type: object
      properties:
        title:
          type: string
        content:
          type: string
        tags:
          type: array
          items:
            type: string
      required: [title, content]

    NoteResponse:
      type: object
      properties:
        id:
          type: string
        title:
          type: string
        content:
          type: string
        tags:
          type: array
          items:
            type: string
        createdAt:
          type: string
          format: date-time
        updatedAt:
          type: string
          format: date-time
      required: [id, title, content, createdAt]

    PagedNoteResponse:
      type: object
      properties:
        content:
          type: array
          items:
            $ref: '#/components/schemas/NoteResponse'
        page:
          type: integer
        size:
          type: integer
        totalElements:
          type: integer
        totalPages:
          type: integer

    ErrorResponse:
      type: object
      properties:
        type:
          type: string
        title:
          type: string
        status:
          type: integer
        detail:
          type: string
        instance:
          type: string



You are an expert frontend engineer specializing in React, TypeScript, and API-driven UI development.  
I already have a backend service built in Java Spring Boot with CRUD REST APIs for managing Attachments and Notes.  
The APIs are OpenAPI 3.0 compliant and follow REST best practices.  

## Your Task
Build a complete React 18+ frontend application that integrates with these APIs.  

### Requirements
1. **Framework & Tools**
   - Use React 18 with TypeScript.
   - Use React Query (TanStack) for API calls, caching, and mutations.
   - Use React Router v6 for navigation.
   - Use Material-UI (MUI) or Tailwind + ShadCN for UI components.
   - Configure Axios as the HTTP client with a baseURL pointing to the Spring Boot API.

2. **Features**
   - **Attachments Module**
     - Upload attachments (file + metadata).
     - List all attachments with details (filename, contentType, size, uploadedBy, uploadedAt).
     - Update attachment metadata (e.g., filename).
     - Delete attachment.
   - **Notes Module**
     - Create, view, update, and delete notes.
     - Notes should be listed with author, content, createdAt, updatedAt.

3. **UI Pages**
   - `/attachments` → show AttachmentForm (upload) + AttachmentList.
   - `/notes` → show NoteForm (create/edit) + NoteList.
   - Navigation bar to switch between Attachments and Notes.

4. **API Integration**
   - All CRUD operations must be wired to backend APIs.
   - For attachments, use `multipart/form-data` when uploading.
   - For notes, use JSON payloads.
   - Automatically refresh UI lists after create/update/delete via React Query cache invalidation.

5. **Error Handling**
   - Show toast/snackbar notifications on success or error.
   - Validate required fields before submitting.

6. **Production-Ready Setup**
   - `package.json` with dependencies preconfigured.
   - Directory structure:
     ```
     src/
      ├── api/            # axios + API client
      ├── components/     # reusable forms, lists
      ├── pages/          # feature pages
      ├── App.tsx
      └── main.tsx
     ```
   - Include sample `.env` with `VITE_API_BASE_URL=http://localhost:8080/api/v1`.
   - Add Dockerfile and instructions in README.md.

7. **Deliverables**
   - Fully working React project.
   - Components and pages as described.
   - Instructions on how to run: `npm install && npm run dev`.

## Input I Will Provide
- Backend API base URL and OpenAPI specification.

## Expected Output
- A complete React frontend codebase wired to my backend APIs, with CRUD screens for Attachments and Notes.




