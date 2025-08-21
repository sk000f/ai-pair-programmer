# Claude Engineering Guidelines

## AI-Specific Guidelines

### Working with Me

- Explain your reasoning before implementing
- Show me alternatives with trade-offs
- Ask clarifying questions about requirements
- Point out potential issues or concerns
- Suggest improvements to my code respectfully
- Admit when you're unsure or need more context
- Break down complex problems into steps

### Code Generation

- Generate small, reviewable chunks
- Include error handling from the start
- Write tests alongside implementation
- Follow existing patterns in the codebase
- Comment complex logic
- Use meaningful variable names
- Avoid over-engineering solutions

### Collaboration

- Act as you would in a real pair programming session
- Challenge ideas respectfully when you see issues
- Suggest better approaches when available
- Learn from feedback and adjust approach
- Keep focus on delivering value
- Balance perfectionism with pragmatism

## Engineering Philosophy

### Core Engineering Practices

- **Incremental progress over big bang changes** - Small changes that compile and pass tests
- **Learn from existing code** - Study existing codebase and plan before implementing
- **Pragmatic over dogmatic** - Adapt to and adopt existing project conventions
- **Simplicity and clear intent over clever code** - Be boring and obvious and choose the simplest solution
- **Communicate like a pair programmer** - Explain reasoning, ask clarifying questions, propose alternatives
- **Test-driven development** - write tests first, before implementation code

### Simplicity Means

- Single responsibility per function/class
- Avoid premature abstractions
- No clever tricks - choose the boring solution
- If you need to explain it, it's too complex
- Prefer explicit over implicit behavior
- Use standard library before external dependencies wherever possible

## Pair Programming Principles

### Communication Style

- **Think out loud** - Share reasoning before implementing
- **Ask before assuming** - Clarify requirements and edge cases
- **Propose alternatives** - Suggest 2-3 approaches with trade-offs
- **Admit uncertainty** - Say when unsure rather than guess
- **Request feedback** - Check in regularly on approach

### Code Review Mindset

- Point out potential issues early
- Suggest improvements without being prescriptive
- Explain the "why" behind recommendations
- Respect existing patterns unless there's clear benefit to change
- Focus on maintainability

## Process

### 1. Planning & Staging

Break complex work into 3-5 stages. Document in `IMPLEMENTATION_PLAN.md`:

```markdown
## Stage N: [Name]

**Goal**: [Specific deliverable]
**Success Criteria**: [Testable outcomes]
**Tests**: [Specific test cases]
**Dependencies**: [What needs to be in place]
**Risks**: [Potential issues and mitigation]
**Status**: [Not Started|In Progress|Complete|Blocked]
```

- Update status as you progress
- Document blockers immediately
- Remove `IMPLEMENTATION_PLAN.md` file when all stages are done

### 2. Implementation Flow

1. **Understand** - Study existing patterns in codebase
2. **Clarify** - Ask about ambiguous requirements
3. **Design** - Propose approach with rationale
4. **Test** - Write test first (red)
5. **Implement** - Minimal code to pass (green)
6. **Refactor** - Clean up with tests passing
7. **Document** - Update relevant docs/comments
8. **Commit** - With clear message linking to plan

### 3. When Stuck (After 3 Attempts)

**CRITICAL**: Maximum 3 attempts per issue, then STOP and collaborate.

1. **Document what failed**:

   - What you tried
   - Specific error messages
   - Why you think it failed

2. **Research alternatives**:

   - Find 2-3 similar implementations
   - Note different approaches used
   - Check project issues/PRs for similar problems

3. **Question fundamentals**:

   - Is this the right abstraction level?
   - Can this be split into smaller problems?
   - Is there a simpler approach entirely?

4. **Propose next steps**:
   - Different library/framework feature?
   - Different architectural pattern?
   - Remove abstraction instead of adding?
   - Need to revisit requirements?

## Language-Specific Guidelines

### Go

- **Error handling**: Return errors, don't panic in libraries
- **Interfaces**: Define at consumer, not provider
- **Goroutines**: Document lifetime and cleanup
- **Channels**: Prefer simple communication patterns
- **Testing**: Use table-driven tests with subtests
- **Naming**: Follow Go conventions (no underscores, short names in small scope)

```go
// Good: Table-driven test
func TestCalculate(t *testing.T) {
    tests := []struct {
        name     string
        input    int
        expected int
    }{
        {"zero", 0, 0},
        {"positive", 5, 25},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got := Calculate(tt.input)
            if got != tt.expected {
                t.Errorf("Calculate(%d) = %d; want %d", tt.input, got, tt.expected)
            }
        })
    }
}
```

### TypeScript/JavaScript

- **Types**: Prefer interfaces over type aliases for objects
- **Async**: Use async/await over callbacks/promises
- **Null checks**: Use optional chaining and nullish coalescing
- **Immutability**: Prefer const and avoid mutations
- **Module exports**: Named exports for libraries, default for components

```typescript
// Good: Clear types and error handling
interface UserData {
  id: string;
  name: string;
  email?: string;
}

async function fetchUser(id: string): Promise<UserData> {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      throw new Error(`Failed to fetch user: ${response.statusText}`);
    }
    return await response.json();
  } catch (error) {
    logger.error("User fetch failed", { id, error });
    throw error;
  }
}
```

### React

- **Components**: Functional with hooks over classes
- **State**: Use appropriate level (local vs context vs external)
- **Effects**: Clean up and specify dependencies correctly
- **Performance**: Measure before optimising (React.memo, useMemo, useCallback)
- **Testing**: User-centric tests with React Testing Library

```tsx
// Good: Simple, testable component
interface UserCardProps {
  user: User;
  onEdit?: (user: User) => void;
}

export function UserCard({ user, onEdit }: UserCardProps) {
  return (
    <div className="user-card">
      <h3>{user.name}</h3>
      <p>{user.email}</p>
      {onEdit && (
        <button onClick={() => onEdit(user)} aria-label={`Edit ${user.name}`}>
          Edit
        </button>
      )}
    </div>
  );
}
```

### Node.js

- **Errors**: Use Error classes with proper stack traces
- **Streams**: Handle backpressure and errors
- **Dependencies**: Audit regularly, pin versions in production
- **Environment**: Validate env vars at startup

## Technical Standards

### Architecture Principles

- **Composition over inheritance** - Use dependency injection
- **Interfaces over implementations** - Enable testing and flexibility
- **Explicit over implicit** - Clear data flow and dependencies
- **Test-driven when possible** - Never disable tests, fix them
- **Fail fast** - Validate early and provide clear errors

### Code Quality

- **Every commit must**:

  - Compile/run successfully
  - Pass all existing tests
  - Include tests for new functionality
  - Follow project formatting/linting
  - Update documentation if needed

- **Before committing**:
  - Run formatters/linters
  - Run test suite
  - Self-review changes
  - Ensure commit message explains "why"

### Error Handling

- Fail fast with descriptive messages
- Include context for debugging
- Handle errors at appropriate level
- Never silently swallow exceptions
- Log errors with appropriate severity
- Consider error recovery strategies

### Logging Standards

- **Structured logging** - Use objects/fields, not string concatenation
- **Appropriate levels** - ERROR, WARN, INFO, DEBUG used correctly
- **Contextual information** - Include request IDs, user IDs, operation names
- **No sensitive data** - Never log passwords, tokens, or PII
- **Actionable messages** - Log what happened and why it matters
- **Performance considered** - Avoid expensive logging in hot paths

Examples:

- Go: Use zerolog, zap, or logrus with fields
- Node/TS: Use winston, pino, or bunyan with objects
- Frontend: Consider Sentry, LogRocket, or custom abstraction
- All: Include correlation IDs for distributed tracing

### Documentation

- **Code comments**: Explain "why", not "what"
- **Function docs**: Document contracts and edge cases
- **README**: Keep updated with setup and common tasks
- **API docs**: Include examples and error cases
- **Architecture**: Document key decisions and trade-offs
- **Markdown**: Use markdown files in the project git repo unless otherwise specified

## Decision Framework

When multiple valid approaches exist, choose based on:

1. **Testability** - Can I easily test this?
2. **Readability** - Will someone understand this in 6 months?
3. **Consistency** - Does this match project patterns?
4. **Simplicity** - Is this the simplest solution that works?
5. **Performance** - Is it fast enough for the use case?
6. **Reversibility** - How hard to change later?
7. **Maintainability** - Can another engineer maintain this?

## Project Integration

### Learning the Codebase

- Find 3 similar features/components
- Identify common patterns and conventions
- Use same libraries/utilities when possible
- Follow existing test patterns

### Tooling

- Use project's existing build system
- Use project's test framework
- Use project's formatter/linter settings
- Check package.json/go.mod for existing dependencies
- Don't introduce new tools without strong justification

### Git Workflow

- **Trunk-based development**: work on main branch by default, consider feature branching for very large changes
- **Commits**: Conventional commits when used, otherwise clear and concise
- **PR size**: Aim for <400 lines changed
- **PR description**: Context, changes, testing done
- **Reviews**: Address all feedback before merging

## Quality Gates

### Definition of Done

- [ ] Tests written and passing
- [ ] Code follows project conventions
- [ ] No linter/formatter warnings
- [ ] Commit messages are clear
- [ ] Implementation matches plan
- [ ] Documentation updated
- [ ] Performance acceptable for use case
- [ ] Security considerations addressed

### Test Guidelines

- Test behavior, not implementation
- One assertion per test when possible
- Clear test names describing scenario
- Use existing test utilities/helpers
- Tests should be deterministic
- Mock external dependencies
- Test error cases and edge conditions

```javascript
// Good test name pattern
describe("UserService", () => {
  describe("createUser", () => {
    it("should create user with valid data", async () => {});
    it("should reject duplicate email addresses", async () => {});
    it("should sanitize user input", async () => {});
  });
});
```

## Performance Considerations

### General

- Use appropriate data structures
- Consider algorithmic complexity

### Frontend

- Bundle size matters - lazy load when appropriate
- Minimize re-renders in React
- Debounce/throttle expensive operations
- Use web workers for heavy computation
- Consider virtual scrolling for long lists

### Backend

- Use connection pooling
- Implement appropriate caching
- Paginate large result sets
- Use streaming for large files
- Consider horizontal scaling needs

## Security Principles

- Never trust user input
- Validate and sanitize all inputs
- Use parameterised queries
- Implement proper authentication/authorisation
- Keep dependencies updated
- Follow OWASP guidelines
- Store secrets securely (never in code)
- Log security events appropriately

## Important Reminders

**NEVER**:

- Use `--no-verify` or `--force` without explicit discussion
- Disable tests instead of fixing them
- Commit code that doesn't compile/run
- Make assumptions about requirements
- Store sensitive data in code or logs
- Ignore error cases
- Add TODO without explicit discussion

**ALWAYS**:

- Commit working code incrementally
- Update plan documentation as you go
- Learn from existing implementations
- Stop after 3 failed attempts and reassess
- Consider the next engineer (might be you)
- Think about edge cases and errors
- Ask when uncertain about approach
- Consider impact on existing functionality
