# Chat Mode for Claude Code

A plugin that transforms Claude Code into a pure conversational assistant. No files created, no commands executed — just deep analysis and thoughtful dialogue.

## What is Chat Mode?

Claude Code is built to take action: create files, run commands, edit code. But sometimes you just want to **talk** — brainstorm ideas, discuss architecture, get explanations, or think through a problem.

`/chat` activates a mode where Claude:

- **Reads and analyzes deeply** before responding
- **Never creates or edits files**
- **Never executes commands**
- **Responds in your language** (English, Portuguese, etc.)
- **Focuses on quality dialogue** — like a senior engineer sitting next to you

## Installation

### Via Marketplace (Recommended)

Add the marketplace and install the plugin directly from GitHub:

```bash
# Step 1: Add the marketplace
/plugins marketplace add eduardofurihata/claude-furihata

# Step 2: Install the plugin
/plugins install chat-mode
```

That's it. No cloning, no local paths.

### Via Git Clone

If you prefer to install manually:

```bash
# Clone the repo
git clone https://github.com/eduardofurihata/claude-furihata.git

# Install the plugin from the chat-skill directory
claude plugins add ./claude-furihata/chat-skill
```

### Verify Installation

```bash
claude plugins list
```

You should see `chat-mode` in the list of installed plugins.

## Usage

Start any message with `/chat` to activate conversation mode:

```
/chat what are the tradeoffs between microservices and monoliths?
```

```
/chat explain how React's reconciliation algorithm works
```

```
/chat explique o padrão CQRS e quando faz sentido usar
```

You can also paste code and discuss it:

```
/chat review this function:

function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
```

Claude will analyze and discuss the code without writing anything to disk.

### Exiting Chat Mode

Chat Mode is active only for the prompt where `/chat` is used. To go back to normal Claude Code behavior, simply send your next message without the `/chat` prefix.

## Examples

| Prompt | What Happens |
|--------|-------------|
| `/chat what is dependency injection?` | Deep explanation with examples, no files created |
| `/chat compare PostgreSQL vs MongoDB for my use case` | Thoughtful analysis of tradeoffs |
| `/chat review this code: [paste]` | Code analysis in dialogue form, nothing written to disk |
| `/chat como funciona o event loop do Node.js?` | Response in Portuguese, same quality |

## How It Works

The plugin registers a `/chat` skill that instructs Claude to operate under strict behavioral constraints:

1. **Tool restrictions** — Write, Edit, Bash, Glob, Grep, and Task tools are blocked
2. **Dialogue focus** — Claude prioritizes deep analysis, multi-perspective reasoning, and natural conversation
3. **Language matching** — Responses match the user's language automatically
4. **Graceful boundaries** — If you ask Claude to create a file during Chat Mode, it gently reminds you to exit the mode first

See [docs/SPEC.md](docs/SPEC.md) for the full technical specification.

## Plugin Structure

```
chat-skill/
├── .claude-plugin/
│   └── plugin.json        # Plugin manifest
├── docs/
│   └── SPEC.md            # Technical specification
├── skills/
│   └── chat/
│       └── SKILL.md       # Skill definition
└── README.md              # This file
```

## Requirements

- Claude Code with plugin support
- No additional dependencies

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

MIT
