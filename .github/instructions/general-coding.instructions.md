---
applyTo: "**"
---
# Project general coding standards

## TypeScript Best Practices
- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain

## Naming Conventions
- Use PascalCase for component names, interfaces, and type aliases
- Use camelCase for variables, functions, and methods
- Prefix private class members with underscore (_)
- Use ALL_CAPS for constants

## Error Handling
- Use try/catch blocks for async operations (but not with Observables)
- Always log errors with contextual information
- Avoid silent failures; always provide feedback to the user

## Code Style
- Use arrow functions instead of function declarations for consistency.
- Prefer const over let unless reassignment is necessary.
- Always use destructuring when accessing props or object properties.
- Keep imports organized and minimal (no unused imports).

## Documentation
- Use JSDoc comments for functions, classes, and complex logic and include:
    - types where applicable
    - usage examples in component-level docstrings if appropriate.
- Maintain a well-documented README file with setup instructions, usage, and contribution guidelines.




