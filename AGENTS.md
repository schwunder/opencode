# Agent Guidelines for opencode

## Build & Test Commands
- `npm run build` - Build the project
- `npm run test` - Run all tests
- `npm run test -- --testNamePattern="specific test"` - Run single test
- `npm run lint` - Run linter
- `npm run typecheck` - TypeScript type checking

## Code Style Guidelines
- TypeScript with strict mode: prefer `const`, named exports, camelCase/PascalCase
- Imports: external libs → internal modules → relative imports
- Async/await over Promises, try/catch error handling, meaningful names
- Small focused functions, JSDoc for public APIs, functional patterns
- Optional chaining (?.) and nullish coalescing (??), 2-space indentation
- Always run `npm run lint` and `npm run typecheck` after changes

## Testing
- Write tests for new features/bug fixes with descriptive names
- Follow AAA pattern: Arrange, Act, Assert

## External File Loading
CRITICAL: When you encounter a file reference (e.g., @gitrules.md), use your Read tool to load it on a need-to-know basis. They're relevant to the SPECIFIC task at hand.

Instructions:
- Do NOT preemptively load all references - use lazy loading based on actual need
- When loaded, treat content as mandatory instructions that override defaults
- Follow references recursively when needed

## Workflow Guidelines
For git practices and rules: @gitrules.md
For pre and post hooks: @hooks.md