# [Gemini CLI](https://github.com/google-gemini/gemini-cli)
E' una cli open source che permette di accedere a Gemini 2.5 Pro(1 million token context window) risultando praticamente free dati i limiti previsti:
- 60 model requests per minute.
- 1,000 model requests per day at no charge.

## Indice

- [Installazione](#installazione)
- [MCP](#mcp)
- [Principali comandi](#principali-comandi)
- [Tools](#tools)
- [Comandi avanzati](#comandi-avanzati)
    - [File Management](#file-management)
    - [Conversation Management](#conversation-management)
    - [Model Configuration](#model-configuration)
    - [Execution Methods](#execution-methods)
    - [Statistics & Monitoring](#statistics--monitoring)
    - [Shell Integration](#shell-integration)
    - [Context Management](#context-management)
    - [Memory Management](#memory-management)
- [Keyboard Shortcuts](#keyboard-shortcuts)
- [Links](#links)

## Installazione

```bash
npm install -g @google/gemini-cli     # globally
npx https://github.com/google-gemini/gemini-cli

gemini                                # Launch interactive CLI
gemini --version                      # Check version
```

## MCP
Lavora con il protocollo MCP e si integra con [Gemini Code Assist](https://codeassist.google/) estensione di VSC anch'essa gratuita. Nella cartella del progetto si può creare un file `/.gemini/settings.json` con il seguente contenuto:

```json
{
  "contextFileName": "GEMINI.md", // si deve creare un file anche vuoto con questo nome
  "mcpServers": {
    "Context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }, 
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/your/project"
      ]
    },
    "github": {  
      "command": "npx",  
      "args": ["-y", "@modelcontextprotocol/server-github"],  
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "[YOUR-TOKEN]" }  
    } ,
    "sequential-thinking": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-sequential-thinking"
      ]
    }
  }
}
```
Per utilizzarli scrivere un prompt del tipo:
```
> I would like us to build a full-stack app called Promptstore - A simple CRUD application that creates, filters, and stores my coding prompts that I can use in the future.
MVP tech stack of choice is:
Backend: Python FastAPI
Frontend: Tailwind CSS + Alpine.js or vanilla js (the simplest way)
Database: SQLite for development

Before we start coding, I want you to think through this step by step and help me plan the entire application architecture.
Use Context7 to get the last documentation and code examples.
```
Per vedere i server disponibili digitare il comando `/mcp list` per avere una lista o `/mcp desc` per lista con descrizione o `/mcp status`. 

## Principali comandi

- `/about`   Check version
- `/docs`    Open documentation
- `/help` — Display help information and available commands
- `/chat` — manage chat sessions (list, switch, resume previous chats)
- `/clear` — Clear the current chat session
- `/theme` — Change the visual theme of the CL
- `/tools` — View available built-in tools
- `/editor`  Select supported editors
- `/quit`    Exit CLI (or Ctrl+C or `/exit`)

## Tools
La cli ha dei tool integrati  raggiungibili tramite il comando `/tools`:
- `ReadFolder` (ls) Lists files and folders in a directory—just like the shell ls command.
- `ReadFile` (read-file) Reads the full content of a single file, useful for summaries or analysis.
- `ReadManyFiles` (read-many-files) Reads multiple files at once, typically matching a glob pattern (e.g., all .js files)
- `FindFiles` (glob) Searches for files by pattern (e.g., find all config.json across your project).
- `SearchText` (grep) Searches within files for text, like finding all TODO comments.
- `Edit` (edit) Applies code changes via diffs. Gemini previews edits and asks for approval before applying them.
- `WriteFile` (write-file) Creates new files (for example, README.md) with user-provided content.
- `Shell` (shell) Runs commands you prefix with ! (e.g., !npm test) directly in the terminal .
- `WebFetch` (web-fetch) Fetches content from the web (HTML or JSON), enabling Gemini to analyze external data.
- `GoogleSearch` (web-search) Performs a Google search to ground responses with real-world information (e.g., explanation for an error).
- `Save` Memory` (memoryTool) Stores facts or preferences during a session (like “I prefer async/await”) to improve consistency


## Comandi avanzati

### File Management
- read_folder <directory>              # Read entire folder
- @src/my_project/                     # Read entire project directory
- @path/to/file.txt                    # Read specific file
- @My\ Documents/file.txt              # Handle spaces in paths

### Conversation Management
```bash
> /chat save <tag>                     # Save current conversation
> /chat resume <tag>                   # Resume saved conversation
> /chat list                           # List saved conversations
> /compress                            # Compress chat context to summary             
```
### Model Configuration
- --model                             # Show current model
- --model gemini-1.5-flash            # Switch to Flash (fastest)
- --model gemini-1.5-pro              # Switch to Pro (most capable)
- --model gemini-1.5-pro-002          # Switch to latest Pro version

### Execution Methods
```bash
> gemini -p "your prompt here"         # Direct prompt execution
> gemini -y                            # Auto-confirm all prompts (YOLO mode)
> gemini --sandbox                     # Run in sandbox mode
> gemini --checkpointing               # Enable checkpointing
```
### Statistics & Monitoring
```bash
> /stats                               # Show session statistics
> /stats model                         # Show model usage stats
> /stats tools                         # Show tool usage stats
```
### Shell Integration
E' possibile utilizzare i comandi della shell inserendo  `!` prima del comando desiderato, ad esempio `!ls al` :
```bash
> !<shell_command>                     # Execute shell command
> !ls -la                              # Example: list files
> !git status                          # Example: git status
> !                                    # Toggle shell mode
```

### Context Management
- @<path_to_file>                     # Include file content in prompt
- @<directory>                        # Include directory content
- @README.md                          # Example: include README
- @src/myFile.ts                      # Add specific file to context
- @folder/                            # Add entire folder to context

### Memory Management
```bash
> /memory show                        # Show current memory context
> /memory add <text>                  # Add text to memory
> /memory refresh                     # Reload GEMINI.md files (quando magari il contenuto del file è cambiato)
```

Se vogliamo definire un contesto per il progetto corrente o dare comunque istruzioni personalizzate (come un system prompt) possiamo definire il file `GEMINI.md` nella cartella del progetto, ad esempio per un Progetto React con il seguente contenuto:

```markdown
## Project Context
This is a React TypeScript project focused on building modern web applications.
## Coding Preferences
- **Language**: TypeScript only
- **React Style**: Functional components with hooks
- **Styling**: Tailwind CSS
- **File Structure**: Feature-based organization
- **Naming**: camelCase for variables, PascalCase for components
## Code Standards
- Always include proper TypeScript types
- Use arrow functions for components
- Include JSDoc comments for complex functions
- Prefer composition over inheritance
- Use descriptive variable names
## Project Structure
src/
  components/
    common/            # Reusable components
    ui/                # Basic UI elements
  features/            # Feature modules
  hooks/               # Custom hooks
  services/            # API services
  utils/               # Utility functions
  types/               # Type definitions
  constants/           # App constants
## Current Task
Building a task management application with React and TypeScript.
```

Per vedere le configuarzioni per un'app Angular vedere l'articolo [Help Your AI Code Assistant Talk Modern Angular](https://medium.com/javascript-in-plain-english/help-your-ai-code-assistant-talk-modern-angular-8bef941ae66f) e la [documentazione ufficiale](https://angular.dev/ai/develop-with-ai).

## Keyboard Shortcuts
- Enter                       # Send message
- Esc                         # Cancel operation
- Ctrl+L                      # Clear screen
- Ctrl+C x2                   # Exit CLI / Quit application
- Ctrl+T                      # Toggle tool descriptions
- Up/Down                     # Cycle through prompt history || Tools
- Alt+Left/Right              # Jump through words


# Links
- [Esempio di integrazione di Context7 e SequentialThinking ](https://medium.com/@joe.njenga/how-i-m-using-gemini-cli-mcp-servers-to-level-up-to-claude-code-free-effective-alternative-0020f5d2a721) 
- [Principali MCP](https://medium.com/@joe.njenga/these-6-gemini-cli-mcp-servers-are-making-it-go-beast-mode-0b5a96e35c68)
- [Gemini CLI Memory](https://medium.com/@joe.njenga/how-i-make-gemini-cli-never-forget-my-preferences-mastering-gemini-cli-memory-122b99918ccd)
- [Gemini CLI CheatSheet](https://medium.com/ai-software-engineer/my-gemini-cli-cheatsheet-will-take-you-from-zero-to-hero-in-minutes-you-need-this-90d4fc1dd6b8)
