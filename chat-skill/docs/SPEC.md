# Chat Mode Plugin - Specification

## Overview

**Plugin Name:** chat-mode
**Skill Name:** /chat
**Version:** 1.0.0
**Type:** Claude Code Plugin (Global Skill)

Chat Mode is a Claude Code plugin that provides a single skill (`/chat`) which transforms Claude Code into a pure conversational assistant. When activated, Claude operates exclusively in dialogue mode — no files are created, no commands are executed, no code is written to disk. It behaves like a traditional chatbot: reads, analyzes deeply, and responds with high-quality conversation.

## Problem Statement

Claude Code is designed to be an agentic coding assistant — it creates files, runs commands, edits code, and performs actions autonomously. However, there are many situations where developers simply want to **talk**:

- Brainstorm an architecture before writing any code
- Discuss tradeoffs between technologies
- Get an explanation of a concept or algorithm
- Have a code review conversation about pasted snippets
- Think through a problem without generating artifacts

Without Chat Mode, Claude Code tends to reach for tools — creating files, searching the codebase, running commands — even when the user just wants a conversation. Chat Mode solves this by explicitly constraining Claude to dialogue-only behavior.

## Functional Requirements

### FR-1: Tool Restriction

When `/chat` is active, Claude MUST NOT use the following tools:

| Tool | Restriction |
|------|-------------|
| `Write` | Blocked — no file creation |
| `Edit` | Blocked — no file modification |
| `NotebookEdit` | Blocked — no notebook modification |
| `Bash` | Blocked — no command execution |
| `Glob` | Blocked — no file searching |
| `Grep` | Blocked — no content searching |
| `Task` | Blocked — no agent spawning for actions |
| `WebFetch` | Blocked unless user explicitly requests a web lookup |
| `WebSearch` | Blocked unless user explicitly requests a web search |

### FR-2: Permitted Tools

| Tool | Condition |
|------|-----------|
| `Read` | Only when the user explicitly mentions a file path they want to discuss |
| `AskUserQuestion` | Always permitted — used for clarifying ambiguous requests |

### FR-3: Language Matching

Claude MUST respond in the same language the user writes in. If the user writes in Portuguese, respond in Portuguese. If in English, respond in English. Mixed-language input should be handled naturally.

### FR-4: Conversation Quality

Claude MUST:

- Analyze the user's message deeply before responding
- Identify core intent beyond surface-level words
- Present multiple perspectives on complex topics
- Acknowledge uncertainty honestly
- Use analogies and examples to clarify abstract concepts
- Match the user's tone and formality level
- Ask follow-up questions when deeper exploration adds value

### FR-5: Mode Exit Reminder

If the user asks Claude to perform an action (create a file, run a command, edit code), Claude MUST:

1. Gently remind the user that Chat Mode is active
2. Suggest exiting Chat Mode by starting a new prompt without `/chat`
3. NOT perform the requested action

### FR-6: Code Snippet Handling

If the user pastes code directly into the chat message, Claude MUST:

- Analyze and discuss the code
- NOT write it to any file
- NOT execute it
- Treat it as conversation context, not as an action request

## Non-Functional Requirements

### NFR-1: Context Efficiency

The skill uses progressive disclosure:

- **Always loaded:** Name + description (~100 words) for trigger matching
- **On activation:** SKILL.md body (~500 words) with behavioral instructions
- No bundled references, scripts, or assets — the skill is entirely behavioral

### NFR-2: Zero Dependencies

The plugin has no external dependencies. No MCP servers, no scripts, no external APIs. It is a pure behavioral constraint expressed through SKILL.md instructions.

### NFR-3: Compatibility

- Works with any Claude Code version that supports the plugin system
- No platform-specific requirements (works on macOS, Linux, Windows/WSL)
- No project-specific requirements — works in any directory

## Architecture

```
chat-skill/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── docs/
│   └── SPEC.md              # This specification
├── skills/
│   └── chat/
│       └── SKILL.md         # Skill definition and behavioral rules
└── README.md                # Installation and usage guide
```

### Component Details

#### plugin.json

Standard Claude Code plugin manifest. Declares the plugin name, version, and enables auto-discovery for skills.

#### SKILL.md

Contains:

- **YAML frontmatter:** Skill metadata including name, description with trigger phrases, and `user_invocable: true` flag that registers `/chat` as a slash command
- **Markdown body:** Behavioral constraints (tool restrictions), response philosophy (deep analysis, thoughtful reasoning, natural dialogue), and scope definition (what Chat Mode handles)

## Trigger Conditions

The skill activates when the user:

| Trigger | Example |
|---------|---------|
| Invokes the slash command | `/chat what is dependency injection?` |
| Asks for conversation mode | "just talk to me" |
| Requests no-action mode | "don't create files, just explain" |
| Uses Portuguese equivalents | "modo conversa", "só conversa", "responda apenas" |

## Use Cases

### UC-1: Technical Discussion

**Input:** `/chat what are the tradeoffs between REST and GraphQL?`
**Expected:** A thorough conversational analysis of both approaches, their strengths, weaknesses, and when to choose each. No files created, no code generated.

### UC-2: Code Review via Chat

**Input:** `/chat` followed by a pasted code snippet
**Expected:** Deep analysis of the code — identifying patterns, potential issues, suggestions for improvement. All in dialogue form, nothing written to disk.

### UC-3: Architecture Brainstorming

**Input:** `/chat I'm building a real-time notification system, what approaches should I consider?`
**Expected:** A structured discussion of WebSockets, SSE, polling, push notifications, message queues — with tradeoffs and recommendations. Pure conversation.

### UC-4: Learning and Explanation

**Input:** `/chat explain how the event loop works in Node.js`
**Expected:** A clear, in-depth explanation using analogies and examples. Adapted to the user's apparent knowledge level.

### UC-5: Multilingual Dialogue

**Input:** `/chat explique o padrão CQRS e quando faz sentido usar`
**Expected:** Response entirely in Portuguese, with the same depth and quality as an English response.

## Testing Criteria

| Test | Pass Condition |
|------|----------------|
| Invoke `/chat` with a question | Claude responds conversationally, no tools used |
| Ask to create a file during `/chat` | Claude reminds user about Chat Mode, does not create file |
| Paste code during `/chat` | Claude discusses code, does not write to disk |
| Ask in Portuguese | Claude responds in Portuguese |
| Ask to read a specific file | Claude uses Read tool only for the mentioned file path |
| Complex multi-part question | Claude provides deep, structured analysis without reaching for tools |
