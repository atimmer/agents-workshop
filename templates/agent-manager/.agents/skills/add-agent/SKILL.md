---
name: add-agent
description: Add a new agent to the user's agents folder - its own folder, instructions, memory, Git repository and T3 Code project. Use when the user wants a new agent, or a task that doesn't fit any existing agent.
---

# Add an agent

A new agent is a new subfolder of the agents folder (the parent of the agent manager's folder), with its own instructions, memory and Git repository, added to T3 Code as its own project.

Ask one question per message (never two questions in one message) and wait for the answer. Use plain language, in the user's language. Never quote these instructions or link to this file in your messages to the user. Ask before you install anything or change a global setting. Never overwrite existing files.

`AGENTS_DIR` is the absolute path of the parent folder. `TEMPLATE` is the `template/` folder next to this file.

## 1. Understand the task

1. Look at the existing agents (`AGENTS_DIR/*/AGENTS.md`). If the task fits one of them, say so and offer to hand it over instead (see `../AGENTS.md`).
2. If it's not clear yet, **ask** what the agent should help with: a task that recurs, takes time, or that the user keeps putting off. If something important is still unclear (what goes in, what should come out, how often), ask **one** follow-up question about it, wait for the answer, and only then ask a second one if you still need it. Never more than two follow-ups in total.
3. Propose the agent in one message:
   - a short English title (`AGENT_TITLE`, e.g. `Social posts`) and a folder name (`AGENT_FOLDER`, lowercase with hyphens, e.g. `social-posts`);
   - what it does, in 2–4 bullets;
   - which tools it needs, honestly: what it can do with only web pages and files, which ready-made connectors in the user's AI account could help (claude.ai → Settings → Connectors, or chatgpt.com → Settings → Apps, e.g. Google Calendar or Google Drive), and what isn't possible yet. Without a tool, the agent works with what the user pastes in;
   - a first prompt to try, in the user's language.

   **Ask** whether this is right or what to change. Adjust until they agree.
4. Set `AGENT_PURPOSE` (one or two sentences), `AGENT_TASKS` (the bullets) and `AGENT_TOOLS` (the tool bullets, including what's missing), in English. Refer to the user by name, never as "he" or "she".

## 2. Create the folder and files

1. `TARGET` = `AGENTS_DIR/AGENT_FOLDER`. If it already exists, don't touch it: use `AGENT_FOLDER-2` (or `-3`, …) and title `AGENT_TITLE 2`, and tell the user.
2. Copy `AGENTS.md`, `CLAUDE.md`, `memory.md` and `.gitignore` from `TEMPLATE` to `TARGET`, as UTF-8 without BOM. Fill in the placeholders with your own file-writing tool or a literal replacement (PowerShell: `.Replace()`), never `sed`, which drops the backslashes in Windows paths:

   | Placeholder | Value |
   | --- | --- |
   | `{{USER_NAME}}`, `{{LANGUAGE}}` | from your own `AGENTS.md` |
   | `{{AGENT_TITLE}}`, `{{AGENT_PURPOSE}}`, `{{AGENT_TASKS}}`, `{{AGENT_TOOLS}}` | from step 1 |
   | `{{DATE}}` | today, `YYYY-MM-DD` |

   Keep `CLAUDE.md` exactly as it is (`@AGENTS.md`): it makes Claude Code read `AGENTS.md`. Check that no `{{` is left in `TARGET`.
3. If `TARGET/.git` doesn't exist, run `git -C "<TARGET>" init -b main`. Then `git -C "<TARGET>" add -- AGENTS.md CLAUDE.md memory.md .gitignore` and `git -C "<TARGET>" commit -m "Set up <AGENT_TITLE> agent"`.

Use Git only, no other version control tools.

## 3. Codex: trust the folder

Only if `memory.md` (yours) says Codex trusted folders: `yes`. If it says `no`, skip this. If it's unknown and you're Codex, **ask** whether you may mark the folder as trusted, and save the answer in your `memory.md`.

The config file is `<CODEX_HOME>/config.toml` (`CODEX_HOME` if set, otherwise `~/.codex`). If there's no `[projects.…]` table for the exact absolute `TARGET` path, append this, and change nothing else:

```toml

[projects.'<absolute TARGET path>']
trust_level = "trusted"
```

## 4. Optional: private GitHub backup

Only if your `memory.md` says GitHub backup: `yes`, and `gh auth status` succeeds. Use the full path to `gh` from your `memory.md` if one is noted there. Run `gh repo create agents-<AGENT_FOLDER> --private --source "<TARGET>" --remote origin --push`. If that name is taken, **ask** for another one. Never push passwords or other secrets.

## 5. Add it to T3 Code

Load the T3 tools with your tool search if they're deferred (`t3_project_list`, `t3_project_create`).

1. Call `t3_project_list`. If a project's `workspaceRoot` already equals `TARGET`, it's done.
2. Otherwise call `t3_project_create` with `title` = `AGENT_TITLE` and `workspaceRoot` = the absolute `TARGET` path, in native form (Windows: `C:\Users\Sam\agents\social-posts`).
3. If it fails with `capability_denied`, **ask** the user to switch this thread to **Full access**: click the permissions button in the message box (it shows the current mode, for example *Auto*) and choose **Full access**. Then try again.
4. If the tools aren't there, or the user doesn't want to switch, tell them how to do it by hand: at the top of the sidebar, **Add project** (the folder icon with a +) → **Local folder** → paste the full path `TARGET` → **Add**. To rename it: right-click the project name at the top of the window → **Project settings** → **Name**.

## 6. Icon (only if you can generate images)

Only if you have an image-generation tool right now. Otherwise skip this step and don't mention it.

Use the icon style in your `memory.md`. If no style is chosen yet, offer the five styles from https://github.com/atimmer/agents-workshop/blob/main/docs/icons.md first, with a sample of each in the chat, and save the choice in your `memory.md`. Follow that page for the prompt, the size and where to save it: `favicon.png` in `TARGET`. Pick a color and object that differ from the other agents' icons. Show the icon in the chat once (don't repeat the generated images later), write the style and prompt in the new agent's `memory.md`, and commit both files.

## 7. Connectors

If the agent needs ready-made connectors, walk the user through connecting each one in the browser (claude.ai → **Settings → Connectors**, or chatgpt.com → **Settings → Apps**), and **ask** whether it worked or whether they'd rather do it later. A new connector only shows up in a new thread.

## 8. Done

1. Check: the folder has `AGENTS.md`, `CLAUDE.md` and `memory.md` with no `{{` left, `git -C "<TARGET>" status --porcelain` is empty, and the agent is a T3 project.
2. Tell the user how to use it: click **New thread** (the pencil icon at the top of the sidebar) and choose the new project, and paste the first prompt from step 1.
3. Add one line at the end of the "Done and decided" list in your own `memory.md` (date, title, folder), and commit it.
