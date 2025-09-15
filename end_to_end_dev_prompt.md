# End-to-End API + React UI Development with Devin.ai

This document consolidates the REST standards, OpenAPI specifications,
DDD guidelines, and frontend integration instructions. It can be used as
a **master reference** or **prompt input** for Devin.ai or other agentic
tools.

------------------------------------------------------------------------

## 1. REST Standards and Best Practices

### General REST Principles

-   **Nouns, not verbs** in URIs\
-   **Resource hierarchy** for sub-resources (e.g.,
    `/attachments/{id}/notes`)\
-   **Plural nouns** for collections\
-   **HTTP Methods mapped to CRUD**
    -   `GET /attachments` → List\
    -   `POST /attachments` → Create\
    -   `GET /attachments/{id}` → Retrieve\
    -   `PUT /attachments/{id}` → Update\
    -   `DELETE /attachments/{id}` → Delete

### OpenAPI Alignment

-   All endpoints defined in **OpenAPI 3.0 YAML/JSON**
-   Use `application/json` for metadata, `multipart/form-data` for
    uploads
-   Error responses aligned with **RFC 7807 Problem Details**

------------------------------------------------------------------------

## 2. Domain Driven Design (DDD) and Microservices

### Spring Boot Project Structure

    src/main/java/com/example/content/
     ├── api/         # Controllers (generated via OpenAPI Generator)
     ├── domain/      # Entities, Value Objects, Aggregates
     ├── dto/         # Data Transfer Objects
     ├── repository/  # Spring Data JPA / Mongo repositories
     ├── service/     # Business logic services
     └── config/      # Spring configs (S3, DB, Security)

### Example Entity: `Attachment`

``` java
@Entity
public class Attachment {
  @Id @GeneratedValue
  private UUID id;
  private String filename;
  private String contentType;
  private long size;
  private String s3Key;
  private String uploadedBy;
  private Instant uploadedAt;
}
```

### Example DTO

``` java
public record AttachmentDto(UUID id, String filename, String contentType, long size,
                            String uploadedBy, Instant uploadedAt) {}
```

------------------------------------------------------------------------

## 3. OpenAPI 3.0 Specification Example

``` yaml
openapi: 3.0.3
info:
  title: User Content Service
  version: 1.0.0
paths:
  /attachments:
    get:
      summary: List attachments
      responses:
        '200':
          description: List of attachments
    post:
      summary: Upload attachment
      requestBody:
        required: true
        content:
          multipart/form-data:
            schema:
              type: object
              properties:
                file:
                  type: string
                  format: binary
                uploadedBy:
                  type: string
      responses:
        '201':
          description: Attachment created
  /attachments/{id}:
    get:
      summary: Get attachment
      parameters:
        - in: path
          name: id
          required: true
          schema: { type: string, format: uuid }
      responses:
        '200':
          description: Attachment metadata
    delete:
      summary: Delete attachment
      parameters:
        - in: path
          name: id
          required: true
          schema: { type: string, format: uuid }
      responses:
        '204':
          description: Deleted
```

------------------------------------------------------------------------

## 4. Prompt for Devin.ai (Backend)

    You are an expert enterprise backend developer with specialization in Java Spring Boot, Domain Driven Design (DDD), OpenAPI 3.0, Microservices, and API-first development.

    Your task is to take:
    1. OpenAPI specification (provided by me)
    2. Database schema specification (Postgres)

    And build a complete backend implementation end-to-end.

    ### Objectives
    1. Use OpenAPI Generator to generate Controllers, DTOs, Entities, Repositories.
    2. Implement DDD layering: Domain, Application, Infrastructure, API.
    3. Ensure Spring Boot 3+, Java 21 compatibility.
    4. Wire APIs to CRUD operations with Postgres and S3 for storage.

    ### Deliverables
    - Full Spring Boot project with generated code
    - Migration scripts
    - Tests (>80% coverage)
    - Docker setup for Postgres + MinIO
    - README.md with instructions

------------------------------------------------------------------------

## 5. Prompt for Devin.ai (Frontend --- React Integration)

    You are an expert frontend engineer specializing in React, TypeScript, and API-driven UI development.
    I already have a backend service built in Spring Boot with CRUD REST APIs for managing Attachments and Notes.

    ### Your Task
    Build a complete React 18+ frontend application that integrates with these APIs.

    ### Requirements
    - Use React 18 + TypeScript
    - Use React Query (TanStack) for API calls and caching
    - Use React Router v6 for navigation
    - Use Material-UI (MUI) or Tailwind+ShadCN for UI
    - Axios client with baseURL

    ### Features
    - Attachments: upload (multipart), list, update, delete
    - Notes: create, list, update, delete
    - Pages: /attachments and /notes
    - Error handling with toast/snackbar

    ### Deliverables
    - Fully working React project
    - package.json preconfigured
    - Directory structure with api/, components/, pages/
    - .env with `VITE_API_BASE_URL=http://localhost:8080/api/v1`
    - Dockerfile + README.md

------------------------------------------------------------------------

## 6. React Integration Code Snippets

### Axios Client

``` ts
// src/api/axiosClient.ts
import axios from "axios";
const axiosClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  headers: { "Content-Type": "application/json" }
});
export default axiosClient;
```

### Attachment Form

``` tsx
function AttachmentForm() {
  const queryClient = useQueryClient();
  const mutation = useMutation(createAttachment, {
    onSuccess: () => queryClient.invalidateQueries(["attachments"])
  });

  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    const formData = new FormData(e.currentTarget);
    mutation.mutate(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" name="file" required />
      <input type="text" name="uploadedBy" placeholder="Uploaded By" />
      <button type="submit">Upload</button>
    </form>
  );
}
```

### Attachment List

``` tsx
function AttachmentList() {
  const queryClient = useQueryClient();
  const { data, isLoading } = useQuery(["attachments"], getAttachments);
  const mutation = useMutation(deleteAttachment, {
    onSuccess: () => queryClient.invalidateQueries(["attachments"])
  });

  if (isLoading) return <p>Loading...</p>;

  return (
    <ul>
      {data?.data.map((att: any) => (
        <li key={att.id}>
          {att.filename}
          <button onClick={() => mutation.mutate(att.id)}>Delete</button>
        </li>
      ))}
    </ul>
  );
}
```

------------------------------------------------------------------------

# ✅ Summary

-   **Section 1--3:** REST, DDD, and OpenAPI standards.\
-   **Section 4:** Prompt for backend codegen with Devin.ai.\
-   **Section 5:** Prompt for frontend React integration with Devin.ai.\
-   **Section 6:** React starter snippets for Attachments/Notes.
