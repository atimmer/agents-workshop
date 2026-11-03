# Agents workshop setup

**You are an AI agent (Claude Code or Codex) running inside T3 Code. Follow these steps in order to set up personal AI agents on this computer for the user.** Each agent gets its own folder, instructions, memory and Git repository. The user is probably not a programmer.

## Rules for you

1. **Ask one thing at a time.** At every **ASK** step, send exactly one question and wait for the answer. Don't continue until you have it.
2. **Use plain language.** Keep messages short and skip jargon. When you install or change something, say in one sentence what it is and why it's needed.
3. **Ask before you install anything or change a global setting.**
4. **Never overwrite or delete the user's files.** If a file or folder you would create already exists, keep it, mention it, and move on. Every step is safe to run again: check what's already done and skip it.
5. **Use the right commands for this computer.** Find out which OS and which shell you're in (step 1.1). Windows commands differ between PowerShell and Git Bash. Always use absolute paths.
6. **If a step fails twice,** stop, explain the problem simply, and offer to skip it. Skipped steps go in the final summary.
7. **Mark anything here that says "UNVERIFIED".** It hasn't been tested on every setup. Try it, and if it doesn't work, use the fallback. Don't assume it worked.
8. **Keep these values** as you go: `LANGUAGE`, `USER_NAME`, `HOME`, `AGENTS_DIR`, `WORKSHOP_DIR`, the agents chosen and their answers, and `PROVIDER` (`claude` if you're Claude Code, `codex` if you're Codex).

Restart after an interruption: if the user starts again in a new thread, run from step 0. The checks make finished steps fast.

---

## Step 0: Language and name

0.1 **ASK**, in Dutch, word for word:

> Hoi! Ik help je om je eigen AI-agents op te zetten. Eerst een vraag: in welke taal wil je dat ik en je agents met je praten? Antwoord met **Nederlands** of **English**.

Set `LANGUAGE` to `Dutch` or `English`. **From now on, write every message in `LANGUAGE`.** Files you create stay in English, except where a step says otherwise.

0.2 **ASK** for the user's first name. It goes into the agents' instructions. Set `USER_NAME`.

0.3 In 2–3 sentences, tell the user what is about to happen: check a few programs, create an `agents` folder, set up one or two agents, add them to T3 Code, and maybe give them icons. Say it takes about 15–30 minutes and that you'll ask before installing anything.

## Step 1: Prerequisites

### 1.1 Detect OS and shell

Run one of these and see which works:

- macOS/Linux/Git Bash: `uname -s && echo "$HOME"`
- PowerShell: `$PSVersionTable.PSVersion; $env:USERPROFILE`

Set `HOME`. On Windows that's the `USERPROFILE` path, e.g. `C:\Users\Sam`. On Windows, Codex uses PowerShell, and Claude Code uses Git Bash if it's installed and PowerShell otherwise. Write commands for the shell you actually have.

### 1.2 Git

Git is "save points for folders": every agent keeps its history in Git. Check with `git --version`.

If it's missing, explain it and **ASK** whether you may install it. Then install it:

- **Windows:** check `winget --version` first. If winget exists, run
  `winget install --id Git.Git -e --source winget --accept-package-agreements --accept-source-agreements`
  Tell the user that a Windows prompt ("Do you want to allow this app…") may appear and that they should click **Yes**.
  - If winget isn't there, ask the user to download and run the installer from https://git-scm.com/download/win with all the default options.
  - A freshly installed Git isn't on this session's PATH yet. Try `& "C:\Program Files\Git\cmd\git.exe" --version`. If that works, use that full path for git for the rest of this setup. If it doesn't, ask the user to fully quit T3 Code (also from the system tray), start it again, open this thread, and type "verder" or "continue". Then start again at step 0.
- **macOS:** run `xcode-select --install`. A dialog appears, and the user clicks **Install**, which takes 5–15 minutes. Wait until the user says it's done, then check `git --version` again. If Homebrew exists (`brew --version`), `brew install git` is a quicker alternative.
- **Linux:** this needs the user's password, so ask the user to open a terminal and run the command for their distro. Then check `git --version`.
  - Debian/Ubuntu: `sudo apt-get update && sudo apt-get install -y git`
  - Fedora: `sudo dnf install -y git`
  - Arch: `sudo pacman -S --noconfirm git`
  - openSUSE: `sudo zypper install -y git`

### 1.3 Git identity

Check `git config --global user.name` and `git config --global user.email`. For each one that's empty, **ASK** for it, then set it with `git config --global user.name "<name>"` or `git config --global user.email "<email>"`. Explain that this only labels the save points. If the user plans to use GitHub, suggest the email address of their GitHub account.

### 1.4 Get the workshop files

Download this repository into a temporary folder. That folder is `WORKSHOP_DIR`.

- macOS/Linux/Git Bash: `WORKSHOP_DIR="${TMPDIR:-/tmp}/agents-workshop"`
- PowerShell: `$WORKSHOP_DIR = "$env:TEMP\agents-workshop"`

If `WORKSHOP_DIR` already exists, run `git -C <WORKSHOP_DIR> pull --ff-only`. If that fails, remove the folder (it's only a temporary download) and clone it again. Otherwise clone it:
`git clone --depth 1 https://github.com/atimmer/agents-workshop <WORKSHOP_DIR>`

If cloning can't work, every file is also at `https://raw.githubusercontent.com/atimmer/agents-workshop/main/<path>`.

Nothing else is needed now. Step 4 installs extra tools only when the user asks for them.

## Step 2: The agents folder

`AGENTS_DIR` = `<HOME>/agents` (Windows: `<HOME>\agents`). Create it if it doesn't exist:

- macOS/Linux/Git Bash: `mkdir -p "$HOME/agents"`
- PowerShell: `New-Item -ItemType Directory -Force "$env:USERPROFILE\agents" | Out-Null`

The folder only holds one subfolder per agent. Don't put files in it, and don't make it a Git repository.

## Step 3: Explain the idea

In `LANGUAGE`, in 2–4 sentences, in your own words:

> Each agent gets its own folder with its own instructions and memory. An AI can only keep a limited amount in mind at once. A bookkeeper doesn't need your budgeting rules, and an email assistant doesn't need to know your WordPress site. Several small, focused agents work better than one agent that has to know everything.

## Step 4: Choose and create agents

There are two example agents. Present them **one at a time**. After each one, **ASK** whether the user wants it.

| Agent | Folder | T3 project title | Template |
| --- | --- | --- | --- |
| WordPress site agent | `wordpress-site` | `WordPress site` | `templates/wordpress-site/` |
| Email assistant | `email-assistant` | `Email assistant` | `templates/email-assistant/` |

### 4.1 WordPress site agent

Explain it: this agent looks at the user's WordPress site in two ways.

- **From the outside** (works with just the URL): it checks the public site against *The Website Specification*, an open checklist for good websites covering SEO, accessibility, security, performance and more.
- **From the inside** (optional): it answers questions about the site's content (posts, pages, categories) through two WordPress plugins. This needs administrator rights, WordPress 6.9 or newer, and permission to install plugins. A test or staging copy of the site is best.

If the user wants it:

1. **ASK** for the site's URL (with `https://`). Set `SITE_URL`.
2. **ASK** whether they also want the inside connection now. Explain they can add it later by asking the agent. Remember the answer for 4.5.

### 4.2 Email assistant

Explain it: this agent helps triage and handle the user's email. It summarises, proposes what to do, and drafts replies. It never sends or deletes anything without the user's OK.

If the user wants it, **ASK** which email they use. Reply honestly based on their answer:

- **Gmail or Google Workspace** (their own domain on Google): ✅ supported. With Workspace, their admin may need to allow the app.
- **Microsoft 365 work or school account**: ⚠️ possible, but an IT administrator usually has to approve the connection first. That probably can't happen during the workshop.
- **Outlook.com / Hotmail / Live** (personal Microsoft): ❌ probably not supported. Claude's Microsoft 365 connector explicitly doesn't support personal accounts, and ChatGPT is untested.
- **Mail from an internet provider or web host** (KPN, Ziggo, own domain over IMAP): ❌ no ready-made connection.

If it's ❌ or ⚠️, **ASK** whether they want to skip this agent, use a Gmail account instead, or create it anyway. If they create it anyway, the agent can still help with emails they paste into the chat, and they can connect a mailbox later. Set `EMAIL_PROVIDER` to a short description, e.g. `Gmail`.

If the user wants no agents, offer the list once more, then go to step 7.

### 4.3 Create each chosen agent folder

For each chosen agent, with `TARGET` = `<AGENTS_DIR>/<folder>` and `TEMPLATE` = `<WORKSHOP_DIR>/templates/<folder>`:

1. Create `TARGET` and any subfolders you need.
2. Copy these files from `TEMPLATE`. Hidden files (starting with `.`) are included, so don't skip them.
   - wordpress-site: `AGENTS.md`, `CLAUDE.md`, `memory.md`, `.gitignore`, `.mcp.json`, `.claude/settings.json`, `.codex/config.toml`
   - email-assistant: `AGENTS.md`, `CLAUDE.md`, `rules.md`, `memory.md`, `.gitignore`

   For each file: **if it already exists in `TARGET`, skip it.** Otherwise read the template file, replace the placeholders below, and write it to `TARGET`. Keep `CLAUDE.md` exactly as it is: it contains `@AGENTS.md` and makes Claude Code read `AGENTS.md`, without needing symlinks, which don't work well on Windows.

   | Placeholder | Value |
   | --- | --- |
   | `{{USER_NAME}}` | `USER_NAME` |
   | `{{LANGUAGE}}` | `LANGUAGE` |
   | `{{SITE_URL}}` | `SITE_URL` (WordPress only) |
   | `{{EMAIL_PROVIDER}}` | `EMAIL_PROVIDER` (email only) |
   | `{{DATE}}` | today, `YYYY-MM-DD` |

3. If `TARGET/.git` doesn't exist, run `git -C "<TARGET>" init -b main`. Check for the `.git` folder itself: some home folders are inside a Git repository already.
4. If `git -C "<TARGET>" status --porcelain` shows anything, run `git -C "<TARGET>" add -A` and then `git -C "<TARGET>" commit -m "Set up <title> agent"`.

Each agent folder is its own Git repository. Use Git only, no other version control tools.

### 4.4 Codex: trust the agent folders

Codex only reads an agent's own `.codex/config.toml` when that folder is marked as trusted. Do this if `PROVIDER` is `codex`, or if the user says they'll also use Codex. Otherwise skip it.

1. The Codex config file is `<CODEX_HOME>/config.toml`. Use the `CODEX_HOME` environment variable if it's set, otherwise `<HOME>/.codex`. Create the file if it doesn't exist.
2. For each agent folder, search the file for its absolute path. If the path isn't there yet, **append** this to the end, and change nothing else:

   ```toml

   [projects.'<absolute TARGET path>']
   trust_level = "trusted"
   ```

   Single quotes keep Windows backslashes as they are, e.g. `[projects.'C:\Users\Sam\agents\email-assistant']`. If the path already has a `[projects…]` section, leave it and tell the user.

UNVERIFIED on Windows: whether Codex matches this path form. Check it in step 7.

### 4.5 Connect the tools

New tools only load when a new thread starts. So the setup thread won't see them; the final check happens in each agent's first thread (step 7).

**WordPress site agent, outside view.** Nothing to do. The template's `.mcp.json` (Claude Code) and `.codex/config.toml` (Codex) already connect the free Website Specification server `https://mcp.specification.website/mcp`, which needs no login. Claude Code loads that project's `.mcp.json` automatically inside T3 Code.

**WordPress site agent, inside view** (only if the user said yes in 4.1). Walk the user through it one action at a time, and wait after each one:

1. The user checks their WordPress version: **Dashboard → Updates**. It must be 6.9 or newer. The site must use `https://`.
2. **Plugins → Add New Plugin**: search for and install + activate **MCP Adapter** (by WordPress.org), then **Enable Abilities for MCP**.
3. **Settings → WP Abilities**: enable only the **Read** abilities. Explain that this way the agent can read but not change anything.
4. **Users → Profile → Application Passwords**: name it `AI agent` → **Add Application Password** → copy the password shown. Explain that it's stored only in this agent's folder on this computer and kept out of Git, and that they can revoke it in the same place at any time.
5. **ASK** for their WordPress username, then **ASK** for the application password.
6. Build the login value `AUTH` = base64 of `username:password`:
   - macOS/Linux/Git Bash: `printf '%s' 'USERNAME:PASSWORD' | base64 | tr -d '\n'`
   - PowerShell: `[Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes('USERNAME:PASSWORD'))`
7. Add a `wordpress` server to both config files in `TARGET`. Keep the `website-spec` entry. If a `wordpress` entry already exists, **ASK** before replacing it. Both files are already in `.gitignore` for this agent.
   - `.mcp.json`: add inside `"mcpServers"`:
     ```json
     "wordpress": {
       "type": "http",
       "url": "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server",
       "headers": { "Authorization": "Basic <AUTH>" }
     }
     ```
   - `.codex/config.toml`: append:
     ```toml

     [mcp_servers.wordpress]
     url = "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server"
     http_headers = { Authorization = "Basic <AUTH>" }
     ```
8. Test it by sending an MCP `initialize` request. The answer should contain `"serverInfo"`.
   - macOS/Linux/Git Bash:
     `curl -s -X POST "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server" -H "Authorization: Basic <AUTH>" -H "Content-Type: application/json" -H "Accept: application/json, text/event-stream" -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"setup-check","version":"1"}}}'`
   - PowerShell, with `curl.exe` (built into Windows 10 and 11):
     ```powershell
     Set-Content -Encoding ascii "$env:TEMP\mcp-init.json" '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"setup-check","version":"1"}}}'
     curl.exe -s -X POST "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server" -H "Authorization: Basic <AUTH>" -H "Content-Type: application/json" -H "Accept: application/json, text/event-stream" --data-binary "@$env:TEMP\mcp-init.json"
     ```

   If it fails, check the response:
   - `401`: wrong username or password.
   - `404` / `rest_no_route`: the MCP Adapter plugin isn't active.
   - An HTML page or `403`: a security plugin or the host is blocking `/wp-json/`.

   Explain the problem simply. The outside view still works without this.

**Email assistant.** The mailbox is connected in the user's AI account in the browser, not in a file:

- `PROVIDER` = `claude`: the user opens https://claude.ai/customize/connectors (on claude.ai: **Settings → Connectors**), clicks **Gmail** (or **Microsoft 365**), then **Connect**, logs in and allows access. Google Calendar is a useful extra. Inside T3 Code, Claude Code automatically gets the claude.ai connectors of the logged-in Claude account. UNVERIFIED: whether that includes Gmail. Claude's docs only promise these connectors on claude.ai and the desktop app.
- `PROVIDER` = `codex`: the user opens https://chatgpt.com → **Settings → Apps** → **Gmail** (or **Outlook**) → **Connect**. UNVERIFIED: whether ChatGPT apps show up in Codex inside T3 Code.

Tell the user that you'll check this in the agent's first thread (step 7). If there are no email tools, the agent will say so and help with pasted emails instead.

### 4.6 Optional: GitHub backup

**ASK** whether the user has a GitHub account, or wants one, to keep a private online backup of their agents. If not, skip this step. Everything works without GitHub.

1. Check `gh --version`. If it's missing, **ASK**, then install it:
   - Windows: `winget install --id GitHub.cli -e --source winget --accept-package-agreements --accept-source-agreements`. Then use `& "C:\Program Files\GitHub CLI\gh.exe"` or restart T3 Code.
   - macOS: `brew install gh`. Without Homebrew, the user installs the `.pkg` from https://cli.github.com.
   - Linux: follow https://github.com/cli/cli#installation. The user runs the `sudo` commands themselves.
2. Login is interactive, so the user does it in their own terminal. Windows: Start → type `PowerShell` → open it. macOS: Cmd+Space → `Terminal`. Linux: their terminal app. They run `gh auth login`, choose **GitHub.com**, **HTTPS**, **Yes** (authenticate Git), and **Login with a web browser**. Then they type the code shown into the browser.
3. Check with `gh auth status`, then run `gh auth setup-git`.
4. For each agent: if `git -C "<TARGET>" remote get-url origin` works, run `git -C "<TARGET>" push`. Otherwise run
   `gh repo create agents-<folder> --private --source "<TARGET>" --remote origin --push`.
   If that name is taken, **ASK** for another one. Always use `--private`.

## Step 5: Add each agent to T3 Code

Each agent becomes its own T3 Code project, with the title from the table and the agent folder as its workspace. Use the first method that works:

**A. T3 tools.** T3 Code gives you MCP tools from a server called `t3-code`: `t3_project_list`, `t3_project_create`, and `t3_project_update`. In Claude Code they're named like `mcp__t3-code__t3_project_create`. They're often *deferred*: if you don't see them, load them with your tool search (Claude Code: ToolSearch with `select:mcp__t3-code__t3_project_list,mcp__t3-code__t3_project_create,mcp__t3-code__t3_project_update`; Codex: search tools for `t3_project`). Then:

1. Call `t3_project_list` (page through `nextCursor`). If a project's `workspaceRoot` already equals `TARGET`, don't create it again. If its title differs, offer to rename it with `t3_project_update`.
2. Otherwise call `t3_project_create` with `title` and `workspaceRoot` = absolute `TARGET` path, in native form (Windows: `C:\Users\Sam\agents\wordpress-site`). Leave out `createWorkspaceRootIfMissing`.

**B. T3 command line** (only if A isn't available; UNVERIFIED inside an agent). The T3 Code desktop app sets `T3CODE_CLI_PATH` to its `t3` launcher, which may or may not reach your shell.

- macOS/Linux/Git Bash: `"$T3CODE_CLI_PATH" project add "<TARGET>" --title "<title>"`
- PowerShell: `& $env:T3CODE_CLI_PATH project add "<TARGET>" --title "<title>"`

If `T3CODE_CLI_PATH` is empty, try `t3 project add …`. "Already exists" means the agent is already registered.

**C. By hand.** Give the user these steps:

1. In T3 Code's left sidebar, click **Add project** (the + button at the top) → **Local folder** → choose `<TARGET>` → confirm.
2. The project is named after the folder. To rename it: right-click the project in the sidebar → **Project settings** → set the title.

## Step 6: Icons (only if you can generate images)

Check honestly whether *you* have an image-generation tool right now. Codex usually has one built in. Claude Code usually doesn't. If you don't have one, say in one sentence that icons need image generation, which you don't have, and that they can add one later in a Codex thread. Then go to step 7.

If you do, follow `<WORKSHOP_DIR>/docs/icons.md`. In short:

1. Show the five styles there, one line each.
2. Generate one sample per style for the first agent.
3. **ASK** which style (1–5) the user wants.
4. Generate one icon per agent in that style, each with its own color and object.
5. Save each as `favicon.png` in the agent folder. If a `favicon.png` already exists, **ASK** before replacing it.
6. Commit it.

T3 Code picks up `favicon.png` from the project folder by itself within a few minutes.

## Step 7: Check and wrap up

Check each item, fix what you can, and report the rest:

- [ ] `AGENTS_DIR` exists and contains one folder per chosen agent.
- [ ] Each agent folder has `AGENTS.md`, `CLAUDE.md` (containing `@AGENTS.md`), and its memory files. The placeholders are replaced: search for `{{`.
- [ ] Each agent folder is its own Git repository with at least one commit. Check with `git -C "<TARGET>" log --oneline -1`.
- [ ] WordPress agent: `.mcp.json` and `.codex/config.toml` exist and are valid. If it has an application password: `git -C "<TARGET>" check-ignore .mcp.json .codex/config.toml` lists both.
- [ ] Codex users: each agent path is in `<CODEX_HOME>/config.toml` with `trust_level = "trusted"`.
- [ ] Each agent is a T3 project (`t3_project_list`, or ask the user to look at the sidebar).
- [ ] Icons, if made: `favicon.png` in each agent folder, committed.
- [ ] GitHub, if chosen: `git -C "<TARGET>" remote get-url origin` works, and the repository is private.

Then send a short closing message in `LANGUAGE` that covers:

1. What was created, and anything skipped and why.
2. **How to use an agent:** in T3 Code's sidebar, right-click the agent's project → **New thread**, and type a question. Always start agent work in that agent's own project, not in this setup thread.
3. A first prompt to try for each agent, in `LANGUAGE`:
   - WordPress site agent: "Check first which tools you have. Then analyse the public side of my site against The Website Specification. Give me a top 5 of improvements, ranked by impact and effort, and explain them for someone who isn't a developer."
   - WordPress, inside view: "Which 5 posts have I published most recently, and which categories do I use most?"
   - Email assistant: "Check first whether you can reach my mailbox. Then look at my unread emails from the past 3 days and suggest what I should do with each one."
4. Mention that each agent remembers what they tell it about their preferences, and saves that in its own folder.
