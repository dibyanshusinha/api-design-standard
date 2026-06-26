# API Design Standard

Version: 0.2.0

## Purpose

All project APIs must expose consistent, secure, and evolvable contracts. OpenAPI specifications are the source of truth and must pass the shared Spectral ruleset before merge.

This standard follows the Google API Design Guide / AIP style:

- Resource-oriented APIs.
- Standard methods first.
- Custom methods only when standard methods do not fit.
- Long-running operations represented consistently.
- Pagination, filtering, sorting, field masks, errors, and versioning handled consistently.

## API-First Rule

Design the OpenAPI contract before implementation. Review the contract with product, frontend, backend, security, platform, and consuming teams before implementation is considered complete.

## Compatibility

Prefer backward-compatible changes. Breaking changes require a new major version, migration notes, and a deprecation period.

Backward-compatible changes usually include:

- Adding optional request fields.
- Adding response fields.
- Adding endpoints.
- Adding enum values only when clients are prepared for unknown values.

Breaking changes include:

- Removing or renaming fields.
- Changing field types or formats.
- Making optional fields required.
- Removing response fields.
- Changing status codes, authentication, pagination, or error semantics.

## OpenAPI Requirements

- OpenAPI 3.0+ is required.
- The OpenAPI file must be committed with the service.
- `info.title`, `info.version`, `info.contact`, and `servers` are required.
- Every operation must include `operationId`, `summary`, `tags`, and at least one 2xx response.
- Operation IDs must use lowerCamelCase.
- APIs should declare root-level security. Public APIs or public operations must explicitly document that choice.

## Resource-Oriented Design

Model APIs around resources, not actions.

Use resource collection paths:

```text
/v1/projects
/v1/projects/{projectId}
/v1/projects/{projectId}/tasks
/v1/projects/{projectId}/tasks/{taskId}
```

Avoid action-style paths:

```text
/v1/getProjects
/v1/project/create
/v1/tasks/search
```

## Naming

Use Google-style lowerCamelCase identifiers:

- Path collection segments: plural lowerCamelCase, such as `projects` or `billingAccounts`.
- Path parameters: lowerCamelCase ending in `Id` where appropriate, such as `projectId`.
- Schema properties: lowerCamelCase.
- Query parameters: lowerCamelCase.
- Operation IDs: lowerCamelCase.

Do not use snake_case or kebab-case in API-visible names unless integrating with an unavoidable external standard.

## Standard Methods

Use standard HTTP methods with resource semantics:

- `GET /v1/projects/{projectId}` gets a resource.
- `GET /v1/projects` lists resources.
- `POST /v1/projects` creates a resource.
- `PATCH /v1/projects/{projectId}` updates a resource.
- `DELETE /v1/projects/{projectId}` deletes a resource.

Avoid `PUT` unless full replacement semantics are intentionally required and documented.

## Custom Methods

Custom methods are allowed only when standard methods do not fit. Use a colon suffix and a verb:

```text
POST /v1/reports/{reportId}:run
POST /v1/jobs/{jobId}:cancel
POST /v1/projects/{projectId}:undelete
```

Custom methods should normally use `POST`.

## Versioning

Public APIs must include a major version in the base path:

```text
/v1/projects
```

Minor compatible changes must not create a new URL version. Breaking changes require a new major version.

## Pagination

List operations that can return unbounded collections must support token-based pagination:

- `pageSize`
- `pageToken`
- `nextPageToken`

List responses should wrap arrays in an object:

```yaml
type: object
properties:
  projects:
    type: array
    items:
      $ref: "#/components/schemas/Project"
  nextPageToken:
    type: string
```

The collection field should normally match the listed resource name, such as `projects`, `tasks`, or `billingAccounts`.

## Filtering and Ordering

Use standard query parameters:

- `filter`: expression for selecting resources.
- `orderBy`: comma-separated sort fields.

Avoid service-specific names like `q`, `query`, `where`, `sort`, or `sort_by`.

## Updates and Field Masks

Partial updates must use `PATCH`. Update operations should include an `updateMask` field or query parameter so clients can specify which fields are being changed.

## Deletes, Undeletes, and Resource State

Delete operations must clearly document whether deletion is soft or permanent. If resources can be restored, use:

```text
POST /v1/projects/{projectId}:undelete
```

Resources with lifecycle state should expose a clear enum such as `state`.

## Long-Running Operations

Operations that cannot complete quickly must return `202 Accepted` and expose an operation resource:

```text
POST /v1/reports/{reportId}:run
GET /v1/operations/{operationId}
POST /v1/operations/{operationId}:cancel
```

Long-running operation responses should reference an `Operation` schema.

## Errors

Use a consistent problem details shape. Prefer RFC 7807:

```yaml
application/problem+json:
  schema:
    $ref: "#/components/schemas/Problem"
```

Minimum fields:

- `type`
- `title`
- `status`
- `detail`
- `instance`

Do not expose stack traces, SQL fragments, exception names, infrastructure IDs, or internal service names in error messages.

## Data Formats

- JSON is the default request and response format.
- Timestamps must use RFC 3339 `date-time`.
- Dates must use RFC 3339 `date`.
- Currency codes must use ISO 4217.
- Country codes must use ISO 3166-1 alpha-2.
- Language codes should use BCP 47.

## Idempotency and Request Identity

Create or custom operations that may be retried should support an idempotency key, request ID, or clearly documented retry behavior.

## Caching and Concurrency

Resources that support optimistic concurrency should expose `etag`. Update and delete operations should document how clients provide preconditions, such as `If-Match`.

## Deprecation

Deprecated operations or fields must include:

- `deprecated: true`
- Replacement guidance in the description
- Planned removal date where possible

## Design Review Checklist

Use this checklist for rules that cannot be fully linted:

- Is the API modeled around resources rather than UI actions?
- Are resources named in terms consumers understand?
- Are standard methods used before custom methods?
- Are custom methods truly necessary?
- Are breaking changes avoided or versioned?
- Are list endpoints paginated from the first release?
- Are long-running operations represented consistently?
- Are update masks used for partial updates?
- Are authorization boundaries clear for each operation?
- Are errors useful without exposing internals?
- Are request retries safe and documented?
- Are examples realistic and safe?

