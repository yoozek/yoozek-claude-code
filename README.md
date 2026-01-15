# Yoozek's Claude Code Setup

Claude Code configuration for productive .NET 10, React, and PostgreSQL development. This plugin provides **14 slash commands** and **11 specialized AI agents** to supercharge your full-stack development workflow.

## Quick Install

```bash
# Step 1: Add the marketplace
/plugin marketplace add yoozek/yoozek-claude-code

# Step 2: Install the plugin
/plugin install yoozek-claude-code
```

## What's Inside

### 📋 Development Commands (7)

- `/new-task` - Analyze task complexity and create implementation plans
- `/code-explain` - Generate detailed explanations
- `/code-optimize` - Performance optimization
- `/code-cleanup` - Refactoring and cleanup
- `/feature-plan` - Feature implementation planning
- `/lint` - Linting and fixes for .NET and React
- `/docs-generate` - Documentation generation

### 🔌 API Commands (3)

- `/api-new` - Create ASP.NET Core Web API endpoints
- `/api-test` - Test Web API endpoints with xUnit
- `/api-protect` - Add authentication & authorization

### 🎨 UI Commands (2)

- `/component-new` - Create React components with TypeScript
- `/page-new` - Create React pages with React Router

### 💾 .NET Commands (2)

- `/ef-model` - Create Entity Framework Core models
- `/ef-migration` - Create and manage EF Core migrations

### 🤖 Specialized AI Agents (11)

**Architecture & Planning**
- **tech-stack-researcher** - Technology choice recommendations with trade-offs
- **system-architect** - Scalable system architecture design
- **backend-architect** - .NET backend systems with data integrity & security
- **frontend-architect** - Performant, accessible React UI architecture
- **requirements-analyst** - Transform ideas into concrete specifications

**Code Quality & Performance**
- **refactoring-expert** - Systematic refactoring and clean code
- **performance-engineer** - Measurement-driven optimization
- **security-engineer** - Vulnerability identification and security standards

**Documentation & Research**
- **technical-writer** - Clear, comprehensive documentation
- **learning-guide** - Teaching programming concepts progressively
- **deep-research-agent** - Comprehensive research with adaptive strategies

## Installation

### From GitHub (Recommended)

```bash
# Add marketplace
/plugin marketplace add yoozek/yoozek-claude-code

# Install plugin
/plugin install yoozek-claude-code
```

### From Local Clone (for development)

```bash
git clone https://github.com/yoozek/yoozek-claude-code.git
cd yoozek-claude-code

# Add as local marketplace
/plugin marketplace add /path/to/yoozek-claude-code

# Install plugin
/plugin install yoozek-claude-code
```

## Best For

- .NET developers
- ASP.NET Core Web API projects
- React developers
- PostgreSQL users
- Entity Framework Core users
- Full-stack engineers
- TypeScript projects
- Developers using Testcontainers for integration tests

## Usage Examples

### Planning a Feature

```bash
/feature-plan
# Then describe your feature idea
```

### Creating a Web API Endpoint

```bash
/api-new
# Claude will scaffold a complete ASP.NET Core endpoint with validation, error handling, and DTOs
```

### Creating Entity Framework Models

```bash
/ef-model User with properties Id, Name, Email, and Orders collection
# Generates EF Core model, configuration, and DTOs
```

### Creating React Components

```bash
/component-new UserProfile component that fetches data from /api/users
# Generates React component with TypeScript, TanStack Query, and proper typing
```

### Research Tech Choices

Just ask Claude questions like:
- "Should I use Redis or in-memory caching for this?"
- "How should I structure my database for multi-tenancy?"
- "What's the best library for PDF generation in .NET?"

The tech-stack-researcher agent automatically activates and provides detailed, researched answers.

## Philosophy

This setup emphasizes:
- **Type Safety**: Strong typing in both C# and TypeScript
- **Best Practices**: Modern .NET 10 and React 19 patterns
- **PostgreSQL**: PostgreSQL as default database with full feature support
- **Testing**: Testcontainers for reliable integration tests
- **Productivity**: Reduces repetitive scaffolding
- **Research**: AI-powered tech decisions with evidence
- **Full-Stack**: Seamless integration between .NET backend and React frontend

## Requirements

- Claude Code 2.0.13+
- Works with any project (optimized for .NET 10 + React + PostgreSQL)
- .NET 10 SDK (for .NET commands)
- Node.js (for React commands)
- Docker (for Testcontainers)

## Customization

After installation, you can customize any command by editing files in `.claude/commands/` and `.claude/agents/`.

## Contributing

Feel free to:
- Fork and customize for your needs
- Submit issues or suggestions
- Share your improvements

## License

MIT - Use freely in your projects

## Author

Created by Yoozek

---

**Note**: Commands are optimized for .NET 10 + React + PostgreSQL workflows with Testcontainers for integration testing.
