# Agents

This folder holds a set of personal AI agents. Each subfolder is one agent. This file explains why it's built this way, for any agent (or person) who wants to know.

## Why separate folders

An AI can only keep a limited amount in mind at once. Everything in its instructions and memory takes up room and attention, even when it has nothing to do with the task at hand.

So each agent gets only the context for its own job. A bookkeeper agent doesn't need the rules an email agent uses to sort newsletters, and an email agent doesn't need to know how the WordPress site is built. Several small, focused agents work better than one agent that has to know everything: they follow their own rules more reliably, make fewer mix-ups, and are easier to fix when something goes wrong.

## What's in each agent folder

- `AGENTS.md`: the agent's instructions: who it works for, what it does, which tools it uses, and its safety rules. `CLAUDE.md` only contains `@AGENTS.md`, so Claude Code reads the same instructions.
- Memory files such as `memory.md` (and `rules.md` for the email assistant): what the agent has learned. The agent updates these itself.
- Tool settings, if the agent has its own tools (`.mcp.json` for Claude Code, `.codex/config.toml` for Codex).
- Its own Git repository, so every change has a save point and can be undone. Optionally backed up to a private GitHub repository.

Each agent is also its own project in T3 Code. Work for an agent starts in a new thread in that agent's project, so it loads only its own folder.

## What's shared

Only this file and `AGENTS.md` in this folder. `AGENTS.md` holds the few rules every agent follows, mainly how to hand work to another agent through T3 Code instead of doing it itself. Keep the shared part small: anything that belongs to one agent goes in that agent's folder.

This folder itself is not a Git repository, and these two files aren't versioned.

## Adding an agent

Add a new agent when a task has its own rules, memory or tools, or when an existing agent's instructions start covering unrelated things. Give it its own subfolder with an `AGENTS.md`, a `CLAUDE.md` containing `@AGENTS.md`, a `memory.md`, and its own Git repository, and add the folder as a T3 Code project. The setup at https://github.com/atimmer/agents-workshop/blob/main/setup.md can do this for you: choose "Define your own workflow".
