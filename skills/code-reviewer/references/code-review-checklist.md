# Code Review Checklist

| Category | Key Questions |
|----------|---------------|
| **Design** | Does it fit existing patterns? Right abstraction level? |
| **Logic** | Edge cases handled? Race conditions? Null checks? |
| **Security** | Input validated? Auth checked? Secrets safe? |
| **Performance** | N+1 queries? Memory leaks? Caching needed? |
| **Tests** | Adequate coverage? Edge cases tested? Mocks appropriate? |
| **Naming** | Clear, consistent, intention-revealing? |
| **Error Handling** | Errors caught? Meaningful messages? Logged? |
| **Documentation** | Public APIs documented? Complex logic explained? |

## Questions to Ask During Code Review

- Is the code well structured and modular?
- Are there any potential bugs or edge cases missed?
- Does the code adhere to the project's coding standards?
- Are there any security vulnerabilities?
- Is the performance of the code optimal?
- Are there sufficient tests, and do they cover edge cases?
- Is the naming of variables, functions, and classes clear and consistent?
- Can the same be achieved with less code or complexity?
- Is error handling implemented correctly?
- Is the code adequately documented for future maintainers?
- Does the code include logging where appropriate?
- Are there other areas of the codebase that do a similar thing that could be reused?
- Is this difficult to be deployed or rolled back if needed?
