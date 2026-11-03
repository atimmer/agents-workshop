# How the agents connect to tools

Background for the workshop host and for agents fixing a connection. The steps themselves are in [`setup.md`](../setup.md). Status as of November 2026. Things marked UNVERIFIED need a live test.

## How T3 Code runs the agents

- **Claude:** T3 Code runs Claude Code through the Claude Agent SDK, in the project folder.
  - It reads `CLAUDE.md`. Our `CLAUDE.md` only contains `@AGENTS.md`, which imports the shared instructions.
  - It loads project MCP servers from `.mcp.json` **without an approval prompt**: SDK runs cannot show the prompt.
  - `.claude/settings.json` also pre-approves them (`enabledMcpjsonServers`), which helps if the same folder is opened in the plain `claude` CLI.
- **Codex:** T3 Code runs the Codex app server in the project folder.
  - It reads `AGENTS.md`.
  - It loads the project's `.codex/config.toml` **only if the folder is trusted**. Trust is set with `[projects.'<path>'] trust_level = "trusted"` in `~/.codex/config.toml` (or in `$CODEX_HOME/config.toml`). T3 Code doesn't set this itself.
- **OAuth for MCP servers:** neither agent can log in to an OAuth MCP server from inside T3 Code. Claude needs `/mcp` or `claude mcp login`, which both need an interactive terminal. Codex needs `codex mcp login`, but T3 usually downloads and manages Codex itself, so the `codex` command often isn't on the PATH. That's why the WordPress connection uses an application password in a header instead of OAuth.

## Per connector

| Connector | Claude Code in T3 | Codex in T3 | Status |
| --- | --- | --- | --- |
| The Website Specification, `https://mcp.specification.website/mcp` (no login; tools `search`, `list_topics`, `get_topic`, `get_checklist`, `get_categories`, `get_changes`; prompt `audit_url`) | `.mcp.json`: `{"type":"http","url":…}` | `.codex/config.toml`: `[mcp_servers.website-spec] url = …` and a trusted folder | Server tested with a live `tools/list`. Agent-side loading uses the same file layout as a working setup on macOS; test it on Windows. |
| WordPress MCP Adapter, `<site>/wp-json/mcp/mcp-adapter-default-server` | `.mcp.json`, `type: http` with `headers.Authorization: Basic base64(user:app-password)` | `.codex/config.toml` with `http_headers = { Authorization = "Basic …" }` | The endpoint and Basic authentication are documented by the MCP Adapter and the `mcp-wordpress-remote` READMEs. UNVERIFIED end to end on shared hosting: security plugins or a WAF can block `/wp-json/`. |
| Gmail / Google Workspace | claude.ai connector (claude.ai → Settings → Connectors). claude.ai connectors show up in Claude Code when you're logged in with a Claude subscription (verified in T3 for a non-Gmail connector). | ChatGPT app (chatgpt.com → Settings → Apps). Codex has apps enabled by default (`features.apps`). | UNVERIFIED: Claude's docs promise Gmail only on claude.ai and Desktop, and T3's managed Codex login may not include ChatGPT apps. Test before the workshop. |
| Microsoft 365 (work) | claude.ai Microsoft 365 connector. Needs one-time consent from an Entra admin. | ChatGPT Outlook app; an admin may need to approve. | Not workshop-friendly. |
| Outlook.com / Hotmail, IMAP (KPN, Ziggo, host) | Not supported. | Unclear (Outlook.com) or not supported (IMAP). | Skip, or use Gmail. |

## Why not the plugin's OAuth?

The **Enable Abilities for MCP** plugin has an OAuth server, but it was built for claude.ai's web connectors, and its client registration for other apps is beta. In T3 Code, the agent can't run an OAuth login (see above). The plugin's own docs say application passwords work for Claude Code and the Codex CLI.

## Secrets

The WordPress application password is stored in the agent's `.mcp.json` and `.codex/config.toml`. The `wordpress-site` template's `.gitignore` excludes both files, so they never reach Git or GitHub. Revoke the password any time in WordPress: Users → Profile → Application Passwords.
