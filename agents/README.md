# Agents

Agents are role-specific personas that provide a focused perspective and output format.
They are not orchestrators — they evaluate and report. Orchestration belongs to slash commands.

## Available Agents

| Agent | Purpose | Invoked via |
|-------|---------|-------------|
| `code-reviewer` | Five-axis code review (correctness, readability, architecture, security, performance) | `/review`, `/ship`, or direct |
| `security-auditor` | Threat-model-focused security review (OWASP, STRIDE, LLM Top 10) | `/review`, `/ship`, or direct |
| `test-engineer` | Test coverage analysis, test design, Prove-It pattern for bugs | `/test`, `/ship`, or direct |

## Composition Rules

1. **Agents do not invoke other agents.** If an agent finds something that warrants a different
   perspective, it surfaces that as a recommendation in its report. The user or a slash command
   decides whether to act on it.

2. **Orchestration belongs to commands.** Commands like `/review` and `/ship` fan out to multiple
   agents, collect their reports, and synthesize the results. Agents never fan out on their own.

3. **Agents are stateless.** Each invocation is independent. An agent does not remember prior
   invocations or share state with other agents.

4. **User-level definitions win.** If a project or global config defines its own `code-reviewer`,
   `security-auditor`, or `test-engineer`, those take precedence over these defaults.

## Frontmatter

Agents use `name` and `description` frontmatter, same as skills. The description should describe
the agent's role and when to use it.

## File Format

Each agent is a single `.md` file in `agents/` with:

- Frontmatter (`name`, `description`)
- Role description (one paragraph)
- Review framework / approach (domain-specific sections)
- Output format (structured template)
- Rules (behavioral constraints)
- Composition section (how this agent is invoked, and the "do not invoke from other agents" rule)