# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Claude Code plugin repository that provides 14 slash commands and 11 specialized AI agents for modern .NET 10, React, and PostgreSQL development, with a focus on ASP.NET Core Web API backend, React frontend, and PostgreSQL database with Testcontainers for integration testing.

## Repository Structure

```
.claude/
├── agents/           # 11 specialized AI agent definitions
├── commands/         # 14 slash command implementations
│   ├── api/         # API-related commands (/api-new, /api-test, /api-protect)
│   ├── misc/        # General development commands
│   ├── dotnet/      # .NET-specific commands (/ef-model, /ef-migration)
│   └── ui/          # React UI component commands
└── settings.template.json

.claude-plugin/
├── plugin.json           # Plugin manifest (commands, agents, MCP servers)
├── marketplace.json      # Marketplace metadata
└── MCP-SERVERS.md       # Documentation for included MCP servers
```

## Plugin Architecture

### Commands
Commands are markdown files with frontmatter that define slash commands. Each command:
- Has a `description` field defining its purpose
- Can specify a `model` (e.g., `claude-sonnet-4-5`)
- Uses `$ARGUMENTS` placeholder for user input
- Follows consistent patterns for output formatting

### Agents
Agents are markdown files with frontmatter that activate automatically based on context. Each agent:
- Has a `name` field (e.g., `tech-stack-researcher`)
- Has a `description` field that defines when the agent should activate
- Can specify a `model` (e.g., `sonnet`, `haiku`)
- Can specify a `color` for UI display
- Contains detailed instructions for the agent's behavior

### MCP Servers
The plugin includes 2 pre-configured MCP servers:
- **context7**: Access up-to-date library documentation
- **playwright**: Browser automation and testing

## Development Workflow

### Making Changes to Commands
1. Edit the command file in `.claude/commands/`
2. Ensure frontmatter is valid YAML
3. Test the command with `/[command-name]`
4. Update `plugin.json` if adding/removing commands

### Making Changes to Agents
1. Edit the agent file in `.claude/agents/`
2. Ensure frontmatter is valid YAML with `name` and `description`
3. Test that agent activates in appropriate contexts
4. Update `plugin.json` if adding/removing agents

### Publishing Updates
1. Make changes to command/agent files
2. Update version in `.claude-plugin/plugin.json` following semver:
   - Patch (2.0.x): Bug fixes and minor tweaks
   - Minor (2.x.0): New commands or agents
   - Major (x.0.0): Breaking changes
3. Commit changes with descriptive message
4. Push to GitHub: `git push`
5. Users update with: `/plugin update edmunds-claude-code`

## Key Design Principles

### Type Safety
- Strong typing in C# (no dynamic types except when necessary)
- Strict TypeScript in React (no `any` types)
- Nullable reference types enabled in .NET
- DTOs matching between backend and frontend

### .NET 10 Best Practices
- Minimal APIs or Controllers for endpoints
- Entity Framework Core 9.0 for data access
- FluentValidation or Data Annotations for validation
- ProblemDetails for consistent error responses
- JWT Bearer authentication
- Dependency injection throughout
- Async/await for I/O operations

### React Best Practices
- Function components with hooks
- TypeScript for all components
- TanStack Query for server state
- React Router v6 for routing
- Component composition over inheritance
- Feature-based organization

### Code Organization
- Feature-based component organization
- Extract business logic to services
- Repository pattern for data access (optional)
- Consistent error handling patterns
- Early validation before expensive operations

### Security
- Input sanitization and validation
- Parameterized queries (EF Core handles this)
- JWT authentication and authorization
- HTTPS only in production
- Never expose sensitive data in responses

## Command Categories

### Development Commands (7)
- `/new-task`: Analyze task complexity and create implementation plan
- `/code-explain`: Generate detailed code explanations
- `/code-optimize`: Performance optimization analysis
- `/code-cleanup`: Refactoring and cleanup
- `/feature-plan`: Feature implementation planning
- `/lint`: Run linting and code analysis for .NET and React
- `/docs-generate`: Generate documentation

### API Commands (3)
- `/api-new`: Create ASP.NET Core Web API endpoints with validation
- `/api-test`: Test endpoints with xUnit and integration tests
- `/api-protect`: Add JWT authentication and authorization

### UI Commands (2)
- `/component-new`: Create React components with TypeScript and TanStack Query
- `/page-new`: Create React Router pages with data fetching

### .NET Commands (2)
- `/ef-model`: Create Entity Framework Core models with relationships
- `/ef-migration`: Create and manage EF Core migrations

## Agent Specializations

### Architecture & Planning
- **tech-stack-researcher**: Technology recommendations for .NET + React projects
- **system-architect**: Scalable system architecture design
- **backend-architect**: .NET backend systems with data integrity
- **frontend-architect**: React UI architecture
- **requirements-analyst**: Transform ideas into specifications

### Code Quality & Performance
- **refactoring-expert**: Systematic refactoring
- **performance-engineer**: Optimization for .NET and React
- **security-engineer**: Security for Web APIs and SPAs

### Documentation & Research
- **technical-writer**: Technical documentation
- **learning-guide**: Teaching programming concepts
- **deep-research-agent**: Comprehensive research

## Common Patterns

### Command Template Structure
```yaml
---
description: Brief description of what the command does
model: claude-sonnet-4-5
---

Command instructions here.

## Requirements
$ARGUMENTS

## Guidelines
...
```

### Agent Template Structure
```yaml
---
name: agent-name
description: When to activate this agent (detailed context)
model: sonnet
color: green
---

You are a [role description].

## Core Responsibilities
...
```

### Response Format (API Commands)
```csharp
// Success
public class ApiResponse<T>
{
    public T? Data { get; set; }
    public bool Success { get; set; } = true;
}

// Error (ProblemDetails)
{
    "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
    "title": "One or more validation errors occurred",
    "status": 400,
    "errors": { ... }
}
```

## Installation Testing

To test the plugin locally:
```bash
# From another project
/plugin marketplace add /path/to/edmunds-claude-code
/plugin install edmunds-claude-code

# Test commands
/api-new
/ef-model

# Uninstall
/plugin uninstall edmunds-claude-code
```

## File Validation

Before committing changes:
1. Validate JSON files have no syntax errors
2. Ensure all paths in `plugin.json` match actual files
3. Test that commands load and execute properly
4. Verify agent activation works as expected
5. Check that MCP servers are properly configured

## Documentation Files

- **README.md**: User-facing plugin documentation and installation instructions
- **QUICK-START.md**: 5-minute guide to publishing the plugin
- **PUBLISHING.md**: Detailed publishing and maintenance guide
- **MCP-SERVERS.md**: Documentation for included MCP servers
- **CLAUDE.md** (this file): Developer guidance for working with the repository

## Technology Stack Focus

This plugin is optimized for:
- .NET 10 backend with ASP.NET Core Web API
- Entity Framework Core 9.0 for data access
- **PostgreSQL** as default database
- React 19 frontend with TypeScript
- React Router v6 for routing
- TanStack Query for server state management
- JWT Bearer authentication
- **Testcontainers** for PostgreSQL integration tests
- C# and TypeScript strong typing throughout

## PostgreSQL-Specific Features

The plugin emphasizes PostgreSQL-specific capabilities:
- Native JSON/JSONB support
- Array types
- UUID primary keys
- Advanced indexing
- Full-text search
- Npgsql provider configuration
