# OpenAPI Specification

## Workflow

1. **Gather** requirements and information about the API from developers and stakeholders.
2. **Draft** the OpenAPI specification in YAML (preferred) or JSON.
3. **Validate** using tooling (e.g. `spectral lint`, `swagger-cli validate`) to ensure OAS compliance.
4. **Review** for accuracy, completeness, and adherence to the project's API style guide.
5. **Publish** and maintain the spec in the repository alongside the API code.

## Rules

- Follow the OpenAPI Specification (OAS) standards strictly.
- Document every endpoint, method, parameter, header, and response.
- Use clear and concise language in descriptions.
- Include examples for request and response payloads (both success and error).
- Keep the spec in sync — update it before deploying any API change.
- Validate the spec in CI to catch drift early.

## Structure Checklist

- [ ] `info` block with title, version, and description
- [ ] `servers` block with environment URLs
- [ ] `paths` with all endpoints and their operations
- [ ] `components/schemas` for reusable request/response models
- [ ] `components/securitySchemes` for authentication
- [ ] `security` applied globally or per-operation
- [ ] `tags` to group related endpoints
- [ ] Response examples for 2xx, 4xx, and 5xx cases
