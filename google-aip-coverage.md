# Google API Design / AIP Coverage

This project aims to implement the Google API Design Guide style for OpenAPI projects. Some rules are enforceable by Spectral, some are partially enforceable, and some require design review.

## Coverage Levels

- `Automated`: enforced by `spectral.yaml`.
- `Partial`: some mechanical checks are enforced, but human review is still required.
- `Review`: cannot be reliably enforced from OpenAPI alone.

## Coverage Matrix

| Area | Coverage | Notes |
| --- | --- | --- |
| Resource-oriented design | Partial | Lints path shape, verbs in paths, standard/custom method patterns. Review still needed for resource model quality. |
| Resource names | Partial | Enforces version prefix, lowerCamelCase path segments, lowerCamelCase path params, and no trailing slashes. |
| Collection identifiers | Partial | Enforces lowerCamelCase path segments. Pluralization is not fully reliable in linting. |
| Standard methods | Partial | Warns on suspicious method/path combinations, `PUT`, request bodies on `GET`, and missing success responses. |
| Custom methods | Partial | Enforces colon-style custom methods and discourages non-`POST` custom methods. |
| Long-running operations | Partial | Checks `202` responses and `Operation` schema conventions where present. |
| Pagination | Automated | Checks `pageSize`, `pageToken`, and `nextPageToken` on likely list endpoints. |
| Filtering | Automated | Encourages `filter` and rejects common custom alternatives. |
| Ordering | Automated | Encourages `orderBy` and rejects common custom alternatives. |
| Field masks / update masks | Partial | Checks `PATCH` operations for `updateMask`. |
| Errors | Automated | Requires documented error responses and `application/problem+json`. |
| Naming conventions | Automated | Checks lowerCamelCase for operation IDs, query params, path params, and schema properties. |
| Versioning | Automated | Checks major version path prefix such as `/v1`. |
| Media types | Automated | Encourages JSON request and response content. |
| Timestamps and dates | Automated | Checks common timestamp/date property suffixes. |
| Security | Partial | Requires root-level security declaration or explicit documented public behavior. Authorization semantics require review. |
| Deprecation | Automated | Requires descriptions for deprecated operations and fields. |
| ETags and concurrency | Partial | Warns when update/delete operations do not expose ETag/precondition patterns. |
| Idempotency / request duplication | Partial | Warns on create/custom operations without idempotency key or request ID parameters. |
| Resource lifecycle / state | Review | Requires product and domain judgment. |
| Batch operations | Review | Must be intentionally designed; difficult to infer from OpenAPI. |
| Partial response / resource views | Review | Depends on client needs and API maturity. |
| Documentation quality | Partial | Checks summaries, descriptions, tags, examples, and contact ownership. |
| Backward compatibility | Review | Requires diffing released API contracts and migration policy. |

## Review-Only Rules

These rules should be checked during API design review:

- The API exposes durable resources, not screen actions or backend implementation details.
- Resource hierarchy is stable and understandable.
- Custom methods cannot be replaced by standard methods.
- Authorization boundaries match the resource hierarchy.
- Long-running operations are necessary and include cancellation or polling where useful.
- Breaking changes are versioned and migration guidance exists.
- Retry behavior is clear for non-idempotent operations.
- Lifecycle state transitions are explicit and documented.

