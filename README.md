# API Design Standard

This repository contains a reusable API design standard for OpenAPI-based projects.

It is aligned with the Google API Design Guide / AIP style where that style can be represented in OpenAPI, and it uses Spectral to enforce the mechanical parts of the standard.

## Contents

- `api-design-standard.md`: human-readable design standard.
- `spectral.yaml`: Spectral ruleset for OpenAPI validation.
- `google-aip-coverage.md`: coverage map showing what is automated, partially automated, or review-only.
- `example-openapi.yaml`: compliant example API.
- `github-actions-openapi-lint.yml`: example GitHub Actions workflow.

## Quick Start

Install Spectral:

```bash
npm install --save-dev @stoplight/spectral-cli
```

Lint an OpenAPI spec:

```bash
npx spectral lint api/openapi.yaml --ruleset spectral.yaml
```

Use in CI:

```bash
npx spectral lint "api/**/*.{yaml,yml,json}" --ruleset spectral.yaml
```

## Philosophy

Not every API design rule can be validated from OpenAPI. Spectral can catch structure, naming, media type, response, pagination, and metadata issues. It cannot reliably decide whether a resource model is conceptually right, whether an operation should exist, or whether an API boundary is product-appropriate.

For that reason this standard has two layers:

- Automated rules in `spectral.yaml`.
- Design-review rules in `google-aip-coverage.md` and `api-design-standard.md`.

## Rule Severity

- `error`: must be fixed before merge.
- `warn`: expected to be fixed or explicitly waived.
- `info`: advisory consistency guidance.

## Recommended Repo Layout

```text
api/
  openapi.yaml
  examples/
  changelog.md
.github/
  workflows/
    openapi-lint.yml
spectral.yaml
```

## Governance

Changes to this standard should include:

- Rationale for the rule.
- Enforcement level: automated, partial, or review-only.
- Migration guidance for existing APIs.
- At least one compliant OpenAPI example.

