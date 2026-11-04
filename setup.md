# Agents workshop setup

**You are an AI agent (Claude Code or Codex) running inside T3 Code. Follow these steps in order to set up personal AI agents on this computer for the user.** Each agent gets its own folder, instructions, memory and Git repository. The user is probably not a programmer.

## Rules for you

1. **One question per message.** At every **ASK** step, send exactly one question and wait for the answer. Never put a second question or request in the same message.
2. **Wait for the user's actions.** When the user has to do something themselves (click, install, log in, connect), ask them to tell you when it's done, and wait. Then check the result if you can.
3. **Use plain language.** Keep messages short and skip jargon. When you install or change something, say in one sentence what it is and why it's needed.
4. **Ask before you install anything or change a global setting** (Git identity, Codex trusted folders).
5. **Never delete or overwrite the user's files.** If a file you would create already exists, keep it. Every step is safe to run again: check what's already done and skip it.
6. **Use the right commands for this computer.** Find out which OS and which shell you're in (step 1.1). Windows commands differ between PowerShell and Git Bash. Always use absolute paths. Shell variables don't survive between your commands, so write the actual paths into each command.
7. **If a step fails twice,** stop, explain the problem simply, and offer to skip it. Skipped steps go in the final checklist.
8. **"UNVERIFIED"** means it hasn't been tested on every setup. Try it, check the result, and use the fallback if it doesn't work.
9. **Keep these values** as you go: `LANGUAGE`, `USER_NAME`, `USER_HOME`, `AGENTS_DIR`, `WORKSHOP_DIR`, the agents chosen and their answers, and `PROVIDER` (`claude` if you're Claude Code, `codex` if you're Codex).

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

Set `USER_HOME` to the printed home folder. On Windows that's the `USERPROFILE` path, e.g. `C:\Users\Sam`. (In PowerShell, don't assign to `$HOME`: it's read-only.) On Windows, Codex uses PowerShell, and Claude Code uses Git Bash if it's installed and PowerShell otherwise.

### 1.2 Git

Git is "save points for folders": every agent keeps its history in Git. On macOS, first tell the user that checking for Git may open a dialog offering to install Apple's developer tools, and that clicking **Install** there is fine. Then check with `git --version`.

If it's missing, explain it and **ASK** whether you may install it. Then install it:

- **Windows:** check `winget --version` first. If winget exists, run
  `winget install --id Git.Git -e --source winget --accept-package-agreements --accept-source-agreements`
  Tell the user that a Windows prompt ("Do you want to allow this app…") may appear and that they should click **Yes**. Without winget, the user downloads and runs the installer from https://git-scm.com/download/win with all the default options.
  - T3 Code only sees a newly installed program after a restart. Ask the user to fully quit T3 Code (also from the system tray), start it again, open this thread, and type "verder" or "continue". Then start again at step 0.
- **macOS:** run `xcode-select --install`. A dialog appears, and the user clicks **Install**, which takes 5–15 minutes. Wait until the user says it's done, then check `git --version` again. If Homebrew exists (`brew --version`), `brew install git` is a quicker alternative.
- **Linux:** this needs the user's password, so ask the user to open a terminal and run the command for their distro. Wait until they say it's done, then check `git --version`.
  - Debian/Ubuntu: `sudo apt-get update && sudo apt-get install -y git`
  - Fedora: `sudo dnf install -y git`
  - Arch: `sudo pacman -S --noconfirm git`
  - openSUSE: `sudo zypper install -y git`

### 1.3 Git identity

Check `git config --global user.name` and `git config --global user.email`. If both are set, move on. For each one that's empty, explain that Git labels every save point on this computer with a name and email, then **ASK** which one to use. Set it with `git config --global user.name "<name>"` or `git config --global user.email "<email>"`. If the user plans to use GitHub, suggest the email address of their GitHub account.

### 1.4 Get the workshop files

Download this repository into a new, empty temporary folder. Never reuse or delete an existing folder.

- macOS/Linux/Git Bash: `D="$(mktemp -d)" && git clone --depth 1 https://github.com/atimmer/agents-workshop "$D/agents-workshop" && echo "$D/agents-workshop"`
- PowerShell: `$D = Join-Path ([IO.Path]::GetTempPath()) ("agents-workshop-" + [guid]::NewGuid().ToString("N").Substring(0, 8)); git clone --depth 1 https://github.com/atimmer/agents-workshop "$D"; $D`

The printed path is `WORKSHOP_DIR` (Git Bash on Windows: `cygpath -w` gives its Windows form). If cloning fails twice, read each file from `https://raw.githubusercontent.com/atimmer/agents-workshop/main/<path>` instead.

## Step 2: The agents folder

`AGENTS_DIR` = `<USER_HOME>/agents` (Windows: `<USER_HOME>\agents`). Create it if it doesn't exist:

- macOS/Linux/Git Bash: `mkdir -p "<USER_HOME>/agents"`
- PowerShell: `New-Item -ItemType Directory -Force "<USER_HOME>\agents" | Out-Null`

Don't make it a Git repository. Besides one subfolder per agent, it holds one shared file, `AGENTS_DIR/AGENTS.md`, which tells every agent how to reach the others through T3 Code. If it doesn't exist yet, copy `<WORKSHOP_DIR>/templates/shared/AGENTS.md` there unchanged (UTF-8 without BOM). If it exists, leave it alone. This file isn't versioned, and Codex doesn't load files outside an agent's own Git repository, so each agent's own `AGENTS.md` points to it as `../AGENTS.md`. Don't add a shared `CLAUDE.md`.

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

1. **ASK** for the site's URL. Set `SITE_URL`: add `https://` if it's missing, and remove any trailing `/` (`https://example.com/news/` → `https://example.com/news`).
2. **ASK** whether they also want the inside connection now. Explain they can add it later by asking the agent. Remember the answer for 4.5.

### 4.2 Email assistant

Explain it: this agent helps triage and handle the user's email. It summarises, proposes what to do, and drafts replies. It never sends or deletes anything without the user's OK.

If the user wants it, **ASK** which email they use. Reply honestly based on their answer:

- **Gmail or Google Workspace** (their own domain on Google): ✅ supported. With Workspace, their admin may need to allow the app.
- **Microsoft 365 work or school account**: ⚠️ possible, but an IT administrator usually has to approve the connection first. That probably can't happen during the workshop.
- **Outlook.com / Hotmail / Live** (personal Microsoft): ❌ probably not supported. Claude's Microsoft 365 connector explicitly doesn't support personal accounts, and ChatGPT is untested.
- **Mail from an internet provider or web host** (KPN, Ziggo, own domain over IMAP): ❌ no ready-made connection.

If it's ❌ or ⚠️, **ASK** whether they want to skip this agent, use a Gmail account instead, or create it anyway. If they create it anyway, the agent can still help with emails they paste into the chat, and they can connect a mailbox later. Set `EMAIL_PROVIDER` to a short description, e.g. `Gmail`.

If the user wants neither agent, say that nothing will be created and go straight to step 7.

### 4.3 Create each chosen agent folder

For each chosen agent, `TARGET` = `<AGENTS_DIR>/<folder>` and `TEMPLATE` = `<WORKSHOP_DIR>/templates/<folder>`.

1. **Check `TARGET` first:**
   - It doesn't exist: create it.
   - It's an agent from an earlier run (its `AGENTS.md` starts with `# WordPress site agent` or `# Email assistant`): use it. WordPress only: if the URL in its `memory.md` differs from `SITE_URL`, **ASK** whether to keep the existing agent for the old site, or create a separate agent for the new site. For a separate agent, use folder `wordpress-site-2` and title `WordPress site 2` (or `-3`, …).
   - Anything else: leave it alone. Use `<folder>-2` (or `-3`, …) and title `<title> 2` instead, and tell the user.
2. Copy these files from `TEMPLATE`. Hidden files (starting with `.`) are included, so don't skip them.
   - wordpress-site: `AGENTS.md`, `CLAUDE.md`, `memory.md`, `.gitignore`, `.mcp.json`, `.claude/settings.json`, `.codex/config.toml`
   - email-assistant: `AGENTS.md`, `CLAUDE.md`, `rules.md`, `memory.md`, `.gitignore`

   For each file: **if it already exists in `TARGET`, keep it** (only fill in placeholders still left in it). Otherwise read the template file, replace the placeholders below, and write it to `TARGET` as UTF-8 without BOM. Keep `CLAUDE.md` exactly as it is: it contains `@AGENTS.md` and makes Claude Code read `AGENTS.md`, without needing symlinks, which don't work well on Windows.

   | Placeholder | Value |
   | --- | --- |
   | `{{USER_NAME}}` | `USER_NAME` |
   | `{{LANGUAGE}}` | `LANGUAGE` |
   | `{{SITE_URL}}` | `SITE_URL` (WordPress only) |
   | `{{EMAIL_PROVIDER}}` | `EMAIL_PROVIDER` (email only) |
   | `{{DATE}}` | today, `YYYY-MM-DD` |

3. If `TARGET/.git` doesn't exist, run `git -C "<TARGET>" init -b main`. Check for the `.git` folder itself: some home folders are inside a Git repository already.
4. Commit only the files from the list above, leaving out any that `.gitignore` lists: `git -C "<TARGET>" add -- <files>`, then `git -C "<TARGET>" commit -m "Set up <title> agent"`. "Nothing to commit" is fine.

Each agent folder is its own Git repository. Use Git only, no other version control tools.

### 4.4 Codex: trust the agent folders

Codex only reads an agent's own `.codex/config.toml` when that folder is marked as trusted. Do this if `PROVIDER` is `codex`, or if the user says they'll also use Codex. Otherwise skip it.

1. Explain this in one sentence and **ASK** whether you may mark the agent folders as trusted for Codex.
2. The Codex config file is `<CODEX_HOME>/config.toml`. Use the `CODEX_HOME` environment variable if it's set, otherwise `<USER_HOME>/.codex`. Create the file if it doesn't exist.
3. For each agent folder, look for a `[projects.…]` table for its exact absolute path (in single or double quotes).
   - **Not there:** append this to the end, and change nothing else:

     ```toml

     [projects.'<absolute TARGET path>']
     trust_level = "trusted"
     ```

     Single quotes keep Windows backslashes as they are: `[projects.'C:\Users\Sam\agents\email-assistant']`. If the path itself contains a `'`, use double quotes and double every backslash: `[projects."C:\\Users\\O'Brien\\agents\\email-assistant"]`.
   - **There with `trust_level = "trusted"`:** nothing to do.
   - **There with another value:** change only that value to `"trusted"`.

UNVERIFIED on Windows: whether Codex matches this path form. The tool check at the end of step 7 shows it.

### 4.5 Connect the tools

New tools only load when a new thread starts. So this setup thread won't see them; step 7 checks them in each agent's own thread.

**WordPress site agent, outside view.** Nothing to do. The template's `.mcp.json` (Claude Code) and `.codex/config.toml` (Codex) already connect the free Website Specification server `https://mcp.specification.website/mcp`, which needs no login. These files hold no secrets and are committed to Git.

**WordPress site agent, inside view** (only if the user said yes in 4.1). Walk the user through it one action at a time, and wait after each one:

1. The user checks their WordPress version: **Dashboard → Updates**. It must be 6.9 or newer. The site must use `https://`. On WordPress.com, the site needs a paid plan, because the free plan can't install plugins.
2. **Plugins → Add New Plugin**: search for and install + activate **MCP Adapter** (by WordPress.org), then **Enable Abilities for MCP**.
3. **Settings → WP Abilities**: the plugin turns most abilities **on** when it's activated, including ones that change the site. Switch off everything except the **Read** abilities, and save. Explain that this way the agent can read but not change anything.
4. **Users → Profile → Application Passwords**: name it `AI agent` → **Add Application Password** → copy the password shown. Explain that they can revoke it in the same place at any time.
5. **ASK** for their WordPress username. Set `WP_USER`.
6. **Keep the password out of Git.** In `TARGET`, add the lines `.mcp.json` and `.codex/config.toml` to `.gitignore` if they aren't there yet. Then run `git -C "<TARGET>" rm --cached -q --ignore-unmatch -- .mcp.json .codex/config.toml`, `git -C "<TARGET>" add -- .gitignore` and `git -C "<TARGET>" commit -m "Keep connection files local"`. The files stay on disk. Check: `git -C "<TARGET>" ls-files -- .mcp.json .codex/config.toml` must print nothing, and `git -C "<TARGET>" check-ignore .mcp.json .codex/config.toml` must print both. If not, stop here.
7. Add a `wordpress` server to both files in `TARGET`, with the literal text `__WORDPRESS_AUTH__` where the password goes. Keep the `website-spec` entry. If a `wordpress` entry already exists, **ASK** before replacing it.
   - `.mcp.json`: add inside `"mcpServers"`:
     ```json
     "wordpress": {
       "type": "http",
       "url": "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server",
       "headers": { "Authorization": "Basic __WORDPRESS_AUTH__" }
     }
     ```
   - `.codex/config.toml`: append:
     ```toml

     [mcp_servers.wordpress]
     url = "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server"
     http_headers = { Authorization = "Basic __WORDPRESS_AUTH__" }
     ```
8. **Get the password without it entering the chat.** Load the T3 tool `request_secret` (Claude Code: ToolSearch `select:mcp__t3-code__request_secret`; Codex: search tools for `request_secret`). Call it with `label` "WordPress application password", a one-sentence `reason`, and `delivery: "file"`. Tell the user to paste the password into the card that appears, not into the chat. It returns `FILE`, a private file with the password. Never print its content.
   - If `request_secret` isn't available, **ASK** for the password in the chat, and say that it will then also be in this conversation. Save it to a file in the system temp folder, use that as `FILE`, and delete that file afterwards.
9. Fill in the password and test the connection in one command. The answer should contain `"serverInfo"`.
   - macOS/Linux/Git Bash:
     ```bash
     cd "<TARGET>" && AUTH="$(printf '%s:%s' "<WP_USER>" "$(cat "<FILE>")" | base64 | tr -d '\n')" && for f in .mcp.json .codex/config.toml; do sed "s|__WORDPRESS_AUTH__|$AUTH|" "$f" > "$f.tmp" && mv "$f.tmp" "$f"; done && curl -s -w '\nHTTP %{http_code}\n' -X POST "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server" -H "Authorization: Basic $AUTH" -H "Content-Type: application/json" -H "Accept: application/json, text/event-stream" -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"setup-check","version":"1"}}}' | head -c 800
     ```
   - PowerShell, with `curl.exe` (built into Windows 10 and 11):
     ```powershell
     $auth = [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("<WP_USER>:" + (Get-Content -Raw "<FILE>").Trim()))
     foreach ($f in "<TARGET>\.mcp.json", "<TARGET>\.codex\config.toml") { [IO.File]::WriteAllText($f, [IO.File]::ReadAllText($f).Replace('__WORDPRESS_AUTH__', $auth)) }
     Set-Content -Encoding ascii "$env:TEMP\mcp-init.json" '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{},"clientInfo":{"name":"setup-check","version":"1"}}}'
     curl.exe -s -w "`nHTTP %{http_code}`n" -X POST "<SITE_URL>/wp-json/mcp/mcp-adapter-default-server" -H "Authorization: Basic $auth" -H "Content-Type: application/json" -H "Accept: application/json, text/event-stream" --data-binary "@$env:TEMP\mcp-init.json"
     ```

   If it fails, check the response:
   - `401`: wrong username or password.
   - `404` / `rest_no_route`: the MCP Adapter plugin isn't active.
   - An HTML page or `403`: a security plugin or the host is blocking `/wp-json/`. On Hostinger this is often the CDN: lower its security level in hPanel, or ask Hostinger support to allow `/wp-json/`.

   Explain the problem simply. The outside view still works without this. To retry with a new password, put `__WORDPRESS_AUTH__` back in both files first.

**Email assistant.** The mailbox is connected in the user's AI account in the browser, not in a file:

- `PROVIDER` = `claude`: the user opens https://claude.ai/customize/connectors (on claude.ai: **Settings → Connectors**), clicks **Gmail** (or **Microsoft 365**), then **Connect**, logs in and allows access. Google Calendar is a useful extra. Claude Code gets the claude.ai connectors of the logged-in Claude account. UNVERIFIED: whether Gmail shows up inside T3 Code.
- `PROVIDER` = `codex`: the user opens https://chatgpt.com → **Settings → Apps** → **Gmail** (or **Outlook**) → **Connect**. UNVERIFIED: whether ChatGPT apps show up in Codex inside T3 Code.

Then **ASK** whether connecting worked, or whether they'd rather do it later. Wait for the answer before you go on. Either way, step 7 checks it in the agent's own thread. Without email tools, the agent helps with pasted emails instead.

### 4.6 Optional: GitHub backup

**ASK** whether the user has a GitHub account, or wants one, to keep a private online backup of their agents. If not, skip this step. Everything works without GitHub.

1. Check `gh --version`. If it's missing, **ASK**, then install it:
   - Windows: `winget install --id GitHub.cli -e --source winget --accept-package-agreements --accept-source-agreements`. T3 Code then needs a restart, as in step 1.2.
   - macOS: `brew install gh`. Without Homebrew, the user installs the `.pkg` from https://cli.github.com.
   - Linux: follow https://github.com/cli/cli#installation. The user runs the `sudo` commands themselves.
2. Login is interactive, so the user does it in their own terminal. Windows: Start → type `PowerShell` → open it. macOS: Cmd+Space → `Terminal`. Linux: their terminal app. They run `gh auth login`, choose **GitHub.com**, **HTTPS**, **Yes** (authenticate Git), and **Login with a web browser**. Then they type the code shown into the browser. Wait until they say it's done, then check with `gh auth status`.
3. For each agent, first check that no password is in its history: `git -C "<TARGET>" log -p -- .mcp.json .codex/config.toml` must not contain `Authorization`. If it does, don't push this agent, and tell the user to revoke that application password in WordPress.
4. Then:
   - **No `origin` yet** (`git -C "<TARGET>" remote get-url origin` fails): run `gh repo create agents-<folder> --private --source "<TARGET>" --remote origin --push`. If that name is taken, **ASK** for another one.
   - **`origin` exists:** run `gh repo view <origin URL> --json nameWithOwner,visibility`. Push with `git -C "<TARGET>" push -u origin main` only if `visibility` is `PRIVATE`. Otherwise don't push, and tell the user which repository it points to.

## Step 5: Add each agent to T3 Code

Skip this step if no agent was created. Each agent becomes its own T3 Code project, with the title from the table and the agent folder as its workspace.

**A. T3 tools.** T3 Code gives you MCP tools from a server called `t3-code`: `t3_project_list`, `t3_project_create`, and `t3_project_update`. They're often *deferred*: load them with your tool search (Claude Code: ToolSearch with `select:mcp__t3-code__t3_project_list,mcp__t3-code__t3_project_create,mcp__t3-code__t3_project_update`; Codex: search tools for `t3_project`). Then:

1. Call `t3_project_list` (page through `nextCursor`). If a project's `workspaceRoot` already equals `TARGET`, don't create it again. If its title differs, offer to rename it with `t3_project_update`.
2. Otherwise call `t3_project_create` with `title` and `workspaceRoot` = absolute `TARGET` path, in native form (Windows: `C:\Users\Sam\agents\wordpress-site`). Leave out `createWorkspaceRootIfMissing`.
3. **If it fails with `capability_denied`:** only a thread in permission mode **Full access** (and **Build**, not Plan) may add projects. **ASK** the user to switch, in `LANGUAGE`, along these lines:
   > Click the model name under the message box. At the bottom of that menu, **Permissions** shows the current mode. Choose **Full access** (the shield with a line through it). If a button under the message box says **Plan**, click it so it says **Build**. Then send me "done".

   Ask only this; don't offer the by-hand route in the same message. The change applies from their next message. Then try A again. If the user doesn't want to switch, go to C.

**B. T3 command line** (only if A's tools don't exist, or A is still denied after the switch). Run `"$T3CODE_CLI_PATH" project add "<TARGET>" --title "<title>"` (PowerShell: `& $env:T3CODE_CLI_PATH project add "<TARGET>" --title "<title>"`). If `T3CODE_CLI_PATH` is empty, go to C. "Already exists" means the agent is already registered.

**C. By hand.** Give the user these steps, with the exact folder path and title for each agent:

1. In T3 Code's left sidebar, click **Add project** (the + button at the top) → **Local folder** → choose `<TARGET>` → confirm.
2. The project is named after the folder. To rename it: right-click the project in the sidebar → **Project settings** → set the title.

Then **ASK** them to tell you when the projects are in the sidebar, or to say "later". Wait for the answer. If `t3_project_list` works, check that each `TARGET` is there before you call it done. Otherwise it's ⚠️ in step 7.

## Step 6: Icons (only if you can generate images)

Skip this step if no agent was created. Check honestly whether *you* have an image-generation tool right now. Codex usually has one built in. Claude Code usually doesn't. If you don't have one, say in one sentence that icons need image generation, which you don't have, and that they can add one later in a Codex thread. Then go to step 7.

If you do, follow `<WORKSHOP_DIR>/docs/icons.md`. In short:

1. Generate one sample per style (five) for the first agent.
2. Show all five in the chat, each as an image labelled with its number and style name, so the user can see them: `![1 – Cut-paper](<absolute path to the PNG>)`. On Windows, use forward slashes in the path (`C:/Users/Sam/…`).
3. **ASK** which style (1–5) the user wants.
4. Generate one icon per agent in that style, each with its own color and object.
5. Save each as `favicon.png` in the agent folder (see icons.md). If a `favicon.png` already exists, **ASK** before replacing it. Show each final icon in the chat the same way as the samples.
6. Add the style and prompt to the agent's `memory.md`, commit `favicon.png` and `memory.md`, and push if the agent has a private GitHub backup.

T3 Code usually shows a new icon within a minute or two. If not, it appears after reloading or restarting T3 Code.

## Step 7: Check and wrap up

Check each item below and fix what you can. Then **show the user the checklist itself**, one line per item: ✅ when it's done and checked, ⚠️ when it's skipped, failed or not checked yet, each ⚠️ with a few words on why. Leave out items that don't apply.

- `AGENTS_DIR` exists, is not a Git repository, and contains the shared `AGENTS.md` plus one folder per chosen agent.
- Each agent folder has `AGENTS.md`, `CLAUDE.md` (containing `@AGENTS.md`), and its memory files. No placeholders are left: search for `{{`.
- Each agent folder is its own Git repository with at least one commit, and `git -C "<TARGET>" status --porcelain` is empty.
- WordPress agent: `.mcp.json` and `.codex/config.toml` exist and are valid. With an inside connection: `git -C "<TARGET>" check-ignore .mcp.json .codex/config.toml` lists both, and the connection test in 4.5 passed.
- Codex users: each agent path is in `<CODEX_HOME>/config.toml` with `trust_level = "trusted"`.
- Email: whether the user connected their mailbox.
- Each agent is a T3 project (checked with `t3_project_list`, or confirmed by the user).
- Icons, if made: `favicon.png` in each agent folder, committed.
- GitHub, if chosen: each agent has a private `origin`, and `git -C "<TARGET>" status -sb` doesn't show `ahead`.

Then send a short closing message in `LANGUAGE` that covers:

1. **How to use an agent:** in T3 Code's sidebar, right-click the agent's project → **New thread**, and type a question. Always start agent work in that agent's own project, not in this setup thread.
2. A first prompt to try for each agent, in `LANGUAGE`:
   - WordPress site agent: "Check first which tools you have. Then analyse the public side of my site against The Website Specification. Give me a top 5 of improvements, ranked by impact and effort, and explain them for someone who isn't a developer."
   - WordPress, inside view: "Which 5 posts have I published most recently, and which categories do I use most?"
   - Email assistant: "Check first whether you can reach my mailbox. Then look at my unread emails from the past 3 days and suggest what I should do with each one."
3. Each agent remembers what they tell it about their preferences, and saves that in its own folder.

Finally, **ASK** the user to try the first prompt now in each agent's own thread, and to come back and tell you whether the agent found its tools (Website Specification, WordPress, mailbox), or to say "later". If a tool is missing, help fix it: Codex needs the trusted folder (4.4), the mailbox is connected in the browser (4.5), and new tools only appear in a new thread.
