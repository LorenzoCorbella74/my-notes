# Copilot Instructions for my-notes Repository

This is a comprehensive technical documentation repository containing notes, tutorials, and examples across multiple technology stacks. The content is primarily in Italian and follows a hierarchical organization pattern.

## Repository Structure & Navigation

**Hierarchical Index System**: The repository uses a systematic index structure:
- Main index: `README.md` at root links to all technology areas
- Technology-specific indices: Each folder has an `indice.md` that links back to main index and lists all topics
- Consistent navigation: `[Torna all'indice principale](../README.md)` appears at the top of each topic index

**Major Technology Areas**:
- `AI/` - AI tools, prompt engineering, coding assistants (GitHub Copilot, Claude, Aider, Ollama, etc.)
- `Angular/` - Architecture patterns, NgRx, RxJS, forms, routing
- `javascript/` - Core JS patterns, decorators, modules, security practices
- `React/`, `Vue3/`, `Svelte/` - Frontend framework specifics
- `CSS/` - Tailwind, Flexbox, Grid, BEM, design fundamentals
- `Tools/` - Development tools, VS Code extensions, build systems
- `Database/` - MongoDB, SQLite, Supabase
- `DevOps/` - CI/CD, deployment strategies

## Content Conventions

**File Organization**: 
- Use descriptive filenames with underscores or hyphens
- Maintain flat folder structures where possible
- Group related content in subfolders (e.g., `AI/MCP/`, `AI/Github Copilot/`)

**Documentation Style**:
- Write in Italian as primary language
- Include practical code examples and commands
- Use markdown code blocks with language specification
- Embed installation/setup commands prominently
- Include links to external resources and repositories

**Technical Examples Pattern**:
```markdown
# Tool/Technology Name
Brief description and purpose

## Installation
```bash
# Clear installation commands
```

## Usage Examples
Practical code snippets with explanations
```

## AI & Development Tools Focus

**AI Coding Assistants**: Extensive coverage of modern AI tools:
- GitHub Copilot usage patterns, chat participants, shortcuts
- Aider for terminal-based pair programming
- Claude Sonnet integration and prompting strategies
- Local LLM setup with Ollama

**Development Workflow**: Focus on practical productivity:
- VS Code extensions and configuration
- Build tools (Vite, NX)
- Testing strategies (Cypress for E2E)
- Terminal commands and automation

## Key Patterns to Follow

1. **Index-First Navigation**: Always maintain bidirectional links between main index and topic indices
2. **Practical Examples**: Include working code snippets and terminal commands
3. **Tool Installation**: Lead with clear setup instructions for any tool or framework
4. **Cross-References**: Link related topics across different technology areas
5. **Italian Documentation**: Maintain Italian as primary language while keeping code/commands in English

## Special Considerations

- **Legacy Content**: Some AngularJS content exists alongside modern Angular - be mindful of version differences
- **Multi-Framework Coverage**: Repository covers competing technologies (React/Vue/Angular) - specify which when making suggestions
- **Tool Diversity**: Wide range of development tools from different ecosystems - consider compatibility when suggesting additions

When working with this repository, prioritize maintaining the existing organizational structure while adding practical, example-driven content that follows the established patterns.