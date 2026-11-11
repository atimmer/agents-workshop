# Agents workshop setup

**You are an AI agent (Claude Code or Codex) running inside T3 Code. Follow these steps in order to set up personal AI agents on this computer for the user.** Each agent gets its own folder, instructions, memory and Git repository. The user is probably not a programmer.

There are two parts:

- **Part 1 (steps 0–4): prepare the computer** and install the **agent manager**, the agent that keeps track of the user's agents and adds new ones. Finish all of it before you mention any workflow.
- **Part 2 (steps 5–11): workflows.** The user picks one workflow at a time, and each one becomes an agent. Afterwards they can pick another.

## Rules for you

1. **One question per message.** At every **ASK** step, send exactly one question and wait for the answer. Never put a second question or request in the same message.
2. **Wait for the user's actions.** When the user has to do something themselves (click, install, log in, connect), ask them to tell you when it's done, and wait. Then check the result if you can.
3. **Use plain language.** Keep messages short and skip jargon. When you install or change something, say in one sentence what it is and why it's needed.
4. **Ask before you install anything or change a global setting** (Git identity, Codex trusted folders). Two exceptions, where you install without asking, after saying in one sentence what you install and why: Git, which every agent needs, and anything an option needs once the user has chosen that option (the GitHub backup needs `gh`). The choice is the permission; don't ask again.
5. **Never delete or overwrite the user's files.** If a file you would create already exists, keep it. Every step is safe to run again: check what's already done and skip it.
6. **Use the right commands for this computer.** Find out which OS and which shell you're in (step 1.1). Windows commands differ between PowerShell and Git Bash. Always use absolute paths. Shell variables don't survive between your commands, so write the actual paths into each command.
7. **If a step fails twice,** stop, explain the problem simply, and offer to skip it. Skipped steps go in the checklists.
8. **"UNVERIFIED"** means it hasn't been tested on every setup. Try it, check the result, and use the fallback if it doesn't work.
9. **Keep these values** as you go: `LANGUAGE`, `USER_NAME`, `USER_HOME`, `AGENTS_DIR`, `WORKSHOP_DIR`, `PROVIDER` (`claude` if you're Claude Code, `codex` if you're Codex), `CODEX_TRUST` and `GITHUB` (step 3), `ICON_STYLE` (step 10), and the agents created so far with their answers.

Restart after an interruption: if the user starts again in a new thread, run from step 0. The checks make finished steps fast.

---

# Part 1: Prepare the computer

## Step 0: Language and name

0.1 **ASK**, in Dutch, word for word:

> Hoi! Ik help je om je eigen AI-agents op te zetten. Eerst een vraag: in welke taal wil je dat ik en je agents met je praten? Antwoord met **Nederlands** of **English**.

Set `LANGUAGE` to `Dutch` or `English`. **From now on, write every message in `LANGUAGE`, including short status updates.** Files you create stay in English, except where a step says otherwise.

0.2 **ASK** for the user's first name. It goes into the agents' instructions. Set `USER_NAME`.

0.3 In 2–3 sentences, tell the user what is about to happen: first you prepare their computer (check a few programs, create an `agents` folder and install an agent manager, about 10 minutes, and you'll ask before installing anything). Then they choose which workflow to build first, and you turn it into an agent. Also tell them that whenever something is unclear, they can just ask you, in their own words.

## Step 1: Prerequisites

### 1.1 Detect OS and shell

Run one of these and see which works:

- macOS/Linux/Git Bash: `uname -s && echo "$HOME"`
- PowerShell: `$PSVersionTable.PSVersion; $env:USERPROFILE`

Set `USER_HOME` to the printed home folder. On Windows that's the `USERPROFILE` path, e.g. `C:\Users\Sam`. (In PowerShell, don't assign to `$HOME`: it's read-only.) On Windows, Codex uses PowerShell, and Claude Code uses Git Bash if it's installed and PowerShell otherwise.

### 1.2 Git

Always start this step by sending this explanation as its own paragraph, before you check anything, even if Git is already installed. Use this text (the Dutch one if `LANGUAGE` is Dutch):

> Al je agents bewaren hun geschiedenis met **Git**, een programma voor versiebeheer. Dat werkt als een automatische reeks bewaarpunten voor een map: elke keer dat een agent zijn instructies of geheugen aanpast, maakt hij een bewaarpunt met een korte notitie van wat er veranderd is. Gaat er iets mis, dan kun je altijd terug naar een eerdere versie, en je kunt altijd zien wat er wanneer veranderd is. Daardoor is het veilig om agents hun eigen bestanden te laten aanpassen.

> Your agents keep their history with **Git**, a version control program. It works like an automatic series of save points for a folder: every time an agent changes its instructions or memory, it makes a save point with a short note of what changed. If something goes wrong, you can always go back to an earlier version, and you can always see what changed and when. That's what makes it safe to let agents change their own files.

On macOS, also tell the user that checking for Git may open a dialog offering to install Apple's developer tools, and that clicking **Install** there is fine. Then check with `git --version`.

If it's missing, say in one sentence that you're installing it now (rule 4: no need to ask). Then install it:

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

Check `git config --global user.name` and `git config --global user.email`. If both are set, move on. Otherwise, first explain in plain words, in `LANGUAGE`: Git signs every save point with who made it, a name and an email address, like a signature under a change. It needs these before it can save anything. They stay on this computer: nobody sees them unless an agent's history is put online, for example in the private GitHub backup later on (which only the user can see). Any name works, and the email doesn't have to be one they check often. Then, for each one that's empty, **ASK** which one to use. Set it with `git config --global user.name "<name>"` or `git config --global user.email "<email>"`. If the user plans to use GitHub, suggest the email address of their GitHub account.

### 1.4 Get the workshop files

Download this repository into a new, empty temporary folder. Never reuse or delete an existing folder.

- macOS/Linux/Git Bash: `D="$(mktemp -d)" && git clone --depth 1 https://github.com/atimmer/agents-workshop "$D/agents-workshop" && echo "$D/agents-workshop"`
- PowerShell: `$D = Join-Path ([IO.Path]::GetTempPath()) ("agents-workshop-" + [guid]::NewGuid().ToString("N").Substring(0, 8)); git clone --depth 1 https://github.com/atimmer/agents-workshop "$D"; $D`

The printed path is `WORKSHOP_DIR` (Git Bash on Windows: `cygpath -w` gives its Windows form). If cloning fails twice, read each file from `https://raw.githubusercontent.com/atimmer/agents-workshop/main/<path>` instead.

## Step 2: The agents folder

`AGENTS_DIR` = `<USER_HOME>/agents` (Windows: `<USER_HOME>\agents`). Create it if it doesn't exist:

- macOS/Linux/Git Bash: `mkdir -p "<USER_HOME>/agents"`
- PowerShell: `New-Item -ItemType Directory -Force "<USER_HOME>\agents" | Out-Null`

Don't make it a Git repository. Besides one subfolder per agent, it holds two shared files from `<WORKSHOP_DIR>/templates/shared/`:

- `README.md` explains why each agent has its own folder: so each agent only gets the context for its own job.
- `AGENTS.md` tells every agent how to reach the others through T3 Code.

For each file: if it doesn't exist in `AGENTS_DIR` yet, copy it there unchanged (UTF-8 without BOM). If it exists, leave it alone. These files aren't versioned, and Codex doesn't load files outside an agent's own Git repository, so each agent's own `AGENTS.md` points to the shared one as `../AGENTS.md`. Don't add a shared `CLAUDE.md`.

## Step 3: Extras for later

### 3.1 Codex: trusted folders

Codex only reads an agent's own `.codex/config.toml` when that folder is marked as trusted. Do this if `PROVIDER` is `codex`, or if the user has said they'll also use Codex. Otherwise set `CODEX_TRUST` to `no` and skip it.

Explain this in one sentence and **ASK** whether you may mark each agent folder you create as trusted for Codex. Set `CODEX_TRUST` to `yes` or `no`. You apply it per agent in step 7.2.

### 3.2 Optional: GitHub backup

**ASK** whether the user wants a private online backup of their agents on GitHub (a free account is enough). If not, set `GITHUB` to `no` and skip this step. Everything works without GitHub. If they want it, ask (as the next question) whether they already have a GitHub account. If not, they make one at https://github.com/signup first; wait until they say it's done.

A yes is permission to install and log in to GitHub's program `gh` (rule 4): don't ask again, just say what you're doing.

1. **Install `gh`** if `gh --version` fails. `GH` is the command you use from now on: `gh`, or its full path.
   - Windows: `winget install --id GitHub.cli -e --source winget --accept-package-agreements --accept-source-agreements`. This thread doesn't see the new program yet, so use the full path `C:\Program Files\GitHub CLI\gh.exe` as `GH` (in PowerShell: `& "C:\Program Files\GitHub CLI\gh.exe"`).
   - macOS with Homebrew (`brew --version` works): `brew install gh`.
   - Otherwise (macOS without Homebrew, Linux, or Windows without winget): download the newest release archive for this OS and processor from https://github.com/cli/cli/releases/latest (`macOS_universal.zip`, `linux_amd64.tar.gz` / `linux_arm64.tar.gz`, `windows_amd64.zip`), unpack it into `<USER_HOME>/.local/gh`, and use the full path to its `bin/gh` (Windows: `bin\gh.exe`) as `GH`. This needs no password.
2. **Log in.** Run `GH auth login --hostname github.com --git-protocol https --web` yourself. It doesn't need a terminal window: it prints a one-time code and the link https://github.com/login/device, and then waits until the user has logged in. Give it at least 10 minutes (a long timeout, or run it in the background and read its output). Show the user the code and the link, and ask them to open the link, enter the code, log in and click **Authorize**, and to tell you when it's done. Then run `GH auth setup-git` and check with `GH auth status`.
   - UNVERIFIED on Windows. If your tool can't keep the command running, the user runs `gh auth login` in their own terminal instead (Windows: Start → `PowerShell`; macOS: Cmd+Space → `Terminal`; Linux: their terminal app), chooses **GitHub.com**, **HTTPS**, **Yes** and **Login with a web browser**, and enters the code shown.

Set `GITHUB` to `yes` if `GH auth status` succeeds, otherwise `no`. Each agent gets its own private repository in step 8.

## Step 4: The agent manager

Every user gets this agent. It knows which agents there are, explains how the folder works, and adds new agents with its `add-agent` skill, so the user doesn't need this setup to grow their agents later.

### 4.1 Create it

Follow steps 7.1, 7.2, 8 and 9 for this agent, then come back here:

| Agent | Folder | T3 project title | Template |
| --- | --- | --- | --- |
| Agent manager | `agent-manager` | `Agent manager` | `templates/agent-manager/` |

Skip 7.3 (it has no tools of its own) and step 10 (it gets its icon together with the first workflow agent, once the user has picked a style). Keep the T3 project id from step 9.

### 4.2 Computer ready

Check these and fix what you can:

- Git works, and `user.name` and `user.email` are set.
- `AGENTS_DIR` exists, is not a Git repository, and contains the shared `README.md` and `AGENTS.md`.
- `WORKSHOP_DIR` contains `setup.md` and `templates/` (or you're reading the files from GitHub).
- Codex users: `CODEX_TRUST` is set. GitHub, if chosen: `GH auth status` succeeds.
- The agent manager: its folder is a Git repository with a commit, and it's a T3 project (or the user is adding it by hand).

### 4.3 Let the agent manager introduce itself

Skip this if the `agent-manager` folder already existed when you reached 4.1 (an earlier run already introduced it); in 4.4, just say the user can ask it anything in its own project. Otherwise: this is part of the setup the user asked for, so you don't need to ask first. Start a new thread in the agent manager's project, where it introduces itself while the user continues here. Load `t3_thread_launch` with your tool search if it's deferred (Claude Code: `select:mcp__t3-code__t3_thread_launch`; Codex: search tools for `t3_thread_launch`). Call it with `projectId` = the agent manager's project id, and leave out model and modes. Use the title and message in `LANGUAGE`, with `USER_NAME` filled in:

- Dutch: title `Kennismaking`, message: `Hoi, ik ben <USER_NAME>. Stel jezelf eens voor: wat doe je, hoe is de map met mijn agents opgebouwd, en hoe voeg ik er later meer agents aan toe?`
- English: title `Introduction`, message: `Hi, I'm <USER_NAME>. Please introduce yourself: what do you do, how is the folder with my agents organised, and how do I add more agents later?`

Don't wait for that thread. If the tool isn't available, fails, or the project isn't in T3 Code yet, give the user that message instead: they click **New thread** (the pencil icon at the top of the sidebar) and choose **Agent manager**, and paste it.

### 4.4 Tell the user

In one message, in `LANGUAGE`:

1. The checklist from 4.2 (✅, or ⚠️ with a few words on why).
2. In one or two sentences: their **Agent manager** is introducing itself in a new thread (mention it with the `link` that `t3_thread_launch` returned), which they can read whenever they like; you continue here. Or, if you couldn't start it, the message to paste.
3. In 2–4 sentences, the idea in your own words:

   > Each workflow gets its own agent: a folder with its own instructions and memory. An AI can only keep a limited amount in mind at once. A bookkeeper doesn't need your budgeting rules, and an email assistant doesn't need to know your WordPress site. Several small, focused agents work better than one agent that has to know everything.

End that message with the question from step 5 (do the folder check from step 5 first).

---

# Part 2: Workflows

## Step 5: Choose a workflow

First list the folders in `AGENTS_DIR`, so you know which agents already exist (from an earlier run, or from an earlier round of this loop). Leave out `agent-manager`: it isn't a workflow.

**ASK** which workflow the user wants to work on first. Show all four options, numbered, with one line each. The first time, use this text (the Dutch one if `LANGUAGE` is Dutch):

> **Your computer is ready. Which workflow do you want to work on first?**
>
> 1. **Email assistant**: helps you clear your inbox and drafts replies. Works best with Gmail or Google Workspace.
> 2. **Your WordPress site, from the inside**: answers questions about your site's posts, pages and categories. You need administrator rights, WordPress 6.9 or newer, and permission to install two plugins. A staging copy of your site is best.
> 3. **Your website, from the outside**: checks the public side of your site for SEO, accessibility, security and speed, against *The Website Specification*. You only need the URL.
> 4. **Define your own workflow**: choose something that would improve your day-to-day the most.
>
> Answer with a number.

> **Je computer is klaar. Aan welke workflow wil je als eerste werken?**
>
> 1. **E-mailassistent**: helpt je je inbox op te ruimen en stelt antwoorden voor. Werkt het best met Gmail of Google Workspace.
> 2. **Je WordPress-site, van binnenuit**: beantwoordt vragen over de berichten, pagina's en categorieën van je site. Je hebt beheerdersrechten nodig, WordPress 6.9 of hoger, en je moet twee plugins kunnen installeren. Een testkopie van je site is het best.
> 3. **Je website, van buitenaf**: controleert de publieke kant van je site op SEO, toegankelijkheid, veiligheid en snelheid, volgens *The Website Specification*. Je hebt alleen de URL nodig.
> 4. **Zelf een workflow bedenken**: kies iets wat jouw dagelijkse werk het meest zou verbeteren.
>
> Antwoord met een nummer.

In later rounds (from step 11), ask "Which workflow do you want to work on next?" instead, and mark the options that are already done with ✅. If agents already existed when you started, mention them in one sentence above the list and mark their options too. Option 4 can be chosen more than once.

Workflows 2 and 3 share one agent, the **WordPress site agent**. It always gets the outside view; workflow 2 adds the inside view. If that agent already exists, workflow 2 only adds the inside connection to it.

| Workflow | Agent | Folder | T3 project title | Template |
| --- | --- | --- | --- | --- |
| 1 | Email assistant | `email-assistant` | `Email assistant` | `templates/email-assistant/` |
| 2, 3 | WordPress site agent | `wordpress-site` | `WordPress site` | `templates/wordpress-site/` |
| 4 | The user's own agent | `<AGENT_FOLDER>` | `<AGENT_TITLE>` | `templates/agent-manager/.agents/skills/add-agent/template/` (the agent manager's `add-agent` skill uses the same files) |

Then go to the matching part of step 6.

## Step 6: Questions for the workflow

### 6.1 Email assistant

Explain it: this agent helps triage and handle the user's email. It summarises, proposes what to do, and drafts replies. It never sends or deletes anything without the user's OK.

**ASK** which email they use. Reply honestly based on their answer:

- **Gmail or Google Workspace** (their own domain on Google): ✅ supported. With Workspace, their admin may need to allow the app.
- **Microsoft 365 work or school account**: ⚠️ possible, but an IT administrator usually has to approve the connection first. That probably can't happen during the workshop.
- **Outlook.com / Hotmail / Live** (personal Microsoft): ❌ probably not supported. Claude's Microsoft 365 connector explicitly doesn't support personal accounts, and ChatGPT is untested.
- **Mail from an internet provider or web host** (KPN, Ziggo, own domain over IMAP): ❌ no ready-made connection.

If it's ❌ or ⚠️, **ASK** whether they want to pick another workflow, use a Gmail account instead, or create it anyway. If they pick another workflow, go back to step 5. If they create it anyway, the agent can still help with emails they paste into the chat, and they can connect a mailbox later. Set `EMAIL_PROVIDER` to a short description, e.g. `Gmail`.

### 6.2 WordPress site, from the inside

Explain it: the agent answers questions about the site's content (posts, pages, categories) through two WordPress plugins, and also checks the public side of the site (workflow 3). Remind them it needs administrator rights, WordPress 6.9 or newer and permission to install plugins, and that a test or staging copy of the site is best.

Set `INSIDE_VIEW` to `yes`. If the WordPress site agent already exists, take `SITE_URL` from its `memory.md`, say which site you'll connect, and go to step 7. (If the user names a different site, 7.1 handles it.) Otherwise **ASK** for the site's URL. Set `SITE_URL`: add `https://` if it's missing, and remove any trailing `/` (`https://example.com/news/` → `https://example.com/news`).

### 6.3 Your website, from the outside

Explain it: the agent checks the public site against *The Website Specification*, an open checklist for good websites covering SEO, accessibility, security, performance and more. It only needs the URL. This agent can later also look inside a WordPress site (workflow 2).

Set `INSIDE_VIEW` to `no`. If the WordPress site agent already exists, say that this workflow is already part of it, and **ASK** whether they want to analyse another site with a separate agent, or pick another workflow (then go back to step 5). For a new or separate agent, **ASK** for the site's URL and set `SITE_URL` as in 6.2.

### 6.4 Define your own workflow

The user designs their own agent. Help them pick something small and concrete that they can try today.

1. **ASK** what would improve their day-to-day the most: a task that recurs, takes time, or that they keep putting off. Give 3 short examples that fit a WordCamp crowd, for example: "turn a new blog post into social media posts", "prepare me for my client meetings", "keep a reading list and summarise what I save".
2. If something important is unclear (what goes in, what should come out, how often), **ASK** one follow-up question. Ask at most two follow-ups in total.
3. Propose the agent in one message:
   - a short English title (`AGENT_TITLE`, e.g. `Social posts`) and a folder name (`AGENT_FOLDER`, lowercase with hyphens, e.g. `social-posts`);
   - what it does, in 2–4 bullets;
   - which tools it needs, honestly: what it can do with only web pages and files (`curl`, your web fetch tool), which ready-made connectors in the user's AI account could help (claude.ai → Settings → Connectors, or chatgpt.com → Settings → Apps, e.g. Google Calendar or Google Drive), and what isn't possible yet. Don't set up custom MCP servers or install software for it during the workshop. Without a tool, the agent works with what the user pastes in;
   - a first prompt to try, in `LANGUAGE`.

   **ASK** whether this is right or what to change. Adjust until they agree.
4. Set `AGENT_PURPOSE` (one or two sentences), `AGENT_TASKS` (the bullets) and `AGENT_TOOLS` (the tool bullets, including what's missing). Write them in English, and refer to the user by name, never as "he" or "she".

## Step 7: Create the agent

`TARGET` = `<AGENTS_DIR>/<folder>` and `TEMPLATE` = `<WORKSHOP_DIR>/<template>` (from the table in step 5, or step 4.1 for the agent manager).

### 7.1 Folder, files and Git

1. **Check `TARGET` first:**
   - It doesn't exist: create it.
   - It's an agent from an earlier run (its `AGENTS.md` starts with `# WordPress site agent`, `# Email assistant`, `# Agent manager`, or `# <AGENT_TITLE>`): use it. WordPress only: if the URL in its `memory.md` differs from `SITE_URL`, **ASK** whether to keep the existing agent for the old site, or create a separate agent for the new site (unless the user already chose a separate agent in 6.3). For a separate agent, use folder `wordpress-site-2` and title `WordPress site 2` (or `-3`, …).
   - Anything else: leave it alone. Use `<folder>-2` (or `-3`, …) and title `<title> 2` instead, and tell the user.
2. Copy these files from `TEMPLATE`. Hidden files (starting with `.`) are included, so don't skip them.
   - wordpress-site: `AGENTS.md`, `CLAUDE.md`, `memory.md`, `.gitignore`, `.mcp.json`, `.claude/settings.json`, `.codex/config.toml`
   - email-assistant: `AGENTS.md`, `CLAUDE.md`, `rules.md`, `memory.md`, `.gitignore`
   - agent-manager: `AGENTS.md`, `CLAUDE.md`, `memory.md`, `.gitignore`, `.agents/skills/add-agent/` (`SKILL.md` and its `template/` folder with `AGENTS.md`, `CLAUDE.md`, `memory.md`, `.gitignore`), `.claude/skills/add-agent/SKILL.md`. Copy the two skill folders **unchanged**: their `{{…}}` placeholders are filled in later, when the agent manager adds an agent.
   - your own agent (workflow 4): `AGENTS.md`, `CLAUDE.md`, `memory.md`, `.gitignore`

   For each file: **if it already exists in `TARGET`, keep it** (only fill in placeholders still left in it). Otherwise read the template file, replace the placeholders below, and write it to `TARGET` as UTF-8 without BOM. Keep `CLAUDE.md` exactly as it is: it contains `@AGENTS.md` and makes Claude Code read `AGENTS.md`, without needing symlinks, which don't work well on Windows.

   | Placeholder | Value |
   | --- | --- |
   | `{{USER_NAME}}` | `USER_NAME` |
   | `{{LANGUAGE}}` | `LANGUAGE` |
   | `{{SITE_URL}}` | `SITE_URL` (WordPress only) |
   | `{{EMAIL_PROVIDER}}` | `EMAIL_PROVIDER` (email only) |
   | `{{CODEX_TRUST}}`, `{{GITHUB}}` | `CODEX_TRUST`, and `GITHUB` plus the full path to `gh` if `GH` isn't just `gh` (agent manager only) |
   | `{{AGENT_TITLE}}`, `{{AGENT_PURPOSE}}`, `{{AGENT_TASKS}}`, `{{AGENT_TOOLS}}` | from step 6.4 (own agent only) |
   | `{{DATE}}` | today, `YYYY-MM-DD` |

3. If `TARGET/.git` doesn't exist, run `git -C "<TARGET>" init -b main`. Check for the `.git` folder itself: some home folders are inside a Git repository already.
4. Commit only the files from the list above (for the skill folders, the folders themselves), leaving out any that `.gitignore` lists: `git -C "<TARGET>" add -- <files>`, then `git -C "<TARGET>" commit -m "Set up <title> agent"`. "Nothing to commit" is fine.

Each agent folder is its own Git repository. Use Git only, no other version control tools.

### 7.2 Codex: trust the folder

Only if `CODEX_TRUST` is `yes`. The Codex config file is `<CODEX_HOME>/config.toml`. Use the `CODEX_HOME` environment variable if it's set, otherwise `<USER_HOME>/.codex`. Create the file if it doesn't exist.

Look for a `[projects.…]` table for the exact absolute `TARGET` path (in single or double quotes).

- **Not there:** append this to the end, and change nothing else:

  ```toml

  [projects.'<absolute TARGET path>']
  trust_level = "trusted"
  ```

  Single quotes keep Windows backslashes as they are: `[projects.'C:\Users\Sam\agents\email-assistant']`. If the path itself contains a `'`, use double quotes and double every backslash: `[projects."C:\\Users\\O'Brien\\agents\\email-assistant"]`.
- **There with `trust_level = "trusted"`:** nothing to do.
- **There with another value:** change only that value to `"trusted"`.

UNVERIFIED on Windows: whether Codex matches this path form. The tool check in step 11 shows it.

### 7.3 Connect the tools

New tools only load when a new thread starts. So this setup thread won't see them; step 11 checks them in the agent's own thread.

**WordPress site agent, outside view.** Nothing to do. The template's `.mcp.json` (Claude Code) and `.codex/config.toml` (Codex) already connect the free Website Specification server `https://mcp.specification.website/mcp`, which needs no login. These files hold no secrets and are committed to Git.

**WordPress site agent, inside view** (only if `INSIDE_VIEW` is `yes`). Walk the user through it one action at a time, and wait after each one:

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

Then **ASK** whether connecting worked, or whether they'd rather do it later. Wait for the answer before you go on. Either way, step 11 checks it in the agent's own thread. Without email tools, the agent helps with pasted emails instead.

**Your own agent.** Only the ready-made connectors from step 6.4, if any. Walk the user through connecting each one in the browser, the same way as for email (claude.ai → **Settings → Connectors**, or chatgpt.com → **Settings → Apps**), and **ASK** whether it worked or whether they'd rather do it later. If no connector is needed, there's nothing to do.

## Step 8: GitHub backup

Only if `GITHUB` is `yes`.

1. First check that no password is in the agent's history: `git -C "<TARGET>" log -p -- .mcp.json .codex/config.toml` must not contain `Authorization`. If it does, don't push this agent, and tell the user to revoke that application password in WordPress.
2. Then:
   - **No `origin` yet** (`git -C "<TARGET>" remote get-url origin` fails): run `GH repo create agents-<folder> --private --source "<TARGET>" --remote origin --push`. If that name is taken, **ASK** for another one.
   - **`origin` exists:** run `GH repo view <origin URL> --json nameWithOwner,visibility`. Push with `git -C "<TARGET>" push -u origin main` only if `visibility` is `PRIVATE`. Otherwise don't push, and tell the user which repository it points to.

## Step 9: Add the agent to T3 Code

Each agent becomes its own T3 Code project, with the title from the table in step 5 (or 4.1) and the agent folder as its workspace.

**A. T3 tools.** T3 Code gives you MCP tools from a server called `t3-code`: `t3_project_list`, `t3_project_create`, and `t3_project_update`. They're often *deferred*: load them with your tool search (Claude Code: ToolSearch with `select:mcp__t3-code__t3_project_list,mcp__t3-code__t3_project_create,mcp__t3-code__t3_project_update`; Codex: search tools for `t3_project`). Then:

1. Call `t3_project_list` (page through `nextCursor`). If a project's `workspaceRoot` already equals `TARGET`, don't create it again. If its title differs, offer to rename it with `t3_project_update`.
2. Otherwise call `t3_project_create` with `title` and `workspaceRoot` = absolute `TARGET` path, in native form (Windows: `C:\Users\Sam\agents\wordpress-site`). Leave out `createWorkspaceRootIfMissing`.
3. **If it fails with `capability_denied`:** only a thread in permission mode **Full access** may add projects. **ASK** the user to switch, in `LANGUAGE`, along these lines:
   > In the message box, click the button that shows the current permissions (for example *Auto*) and choose **Full access**. Then send me "done".

   Ask only this; don't offer the by-hand route in the same message. The change applies from their next message. Then try A again. If the user doesn't want to switch, go to C.

**B. T3 command line** (only if A's tools don't exist, or A is still denied after the switch). Run `"$T3CODE_CLI_PATH" project add "<TARGET>" --title "<title>"` (PowerShell: `& $env:T3CODE_CLI_PATH project add "<TARGET>" --title "<title>"`). If `T3CODE_CLI_PATH` is empty, go to C. "Already exists" means the agent is already registered.

**C. By hand.** Give the user these steps, with the exact folder path and title:

1. At the top of T3 Code's sidebar, click **Add project** (the folder icon with a +) → **Local folder** → paste the full path `<TARGET>` → **Add**.
2. The project is named after the folder. To rename it: right-click the project name at the top of the window → **Project settings** → change **Name**, then press Tab.

Then **ASK** them to tell you when the project is added, or to say "later". Wait for the answer. If `t3_project_list` works, check that `TARGET` is there before you call it done. Otherwise it's ⚠️ in step 11.

## Step 10: Icon (only if you can generate images)

Check honestly whether *you* have an image-generation tool right now. Codex usually has one built in. Claude Code usually doesn't. If you don't have one, say in one sentence (only the first time) that icons need image generation, which you don't have, and that they can add one later in a Codex thread. Then go to step 11.

If you do, follow `<WORKSHOP_DIR>/docs/icons.md`. In short:

1. **If `ICON_STYLE` isn't set yet** (this is the first agent with an icon):
   1. Generate one sample per style (five) for this agent.
   2. Show all five in the chat, each as an image labelled with its number and style name, so the user can see them: `![1 – Cut-paper](<absolute path to the PNG>)`. On Windows, use forward slashes in the path (`C:/Users/Sam/…`).
   3. **ASK** which style (1–5) the user wants. Set `ICON_STYLE`.
   4. Also make the agent manager's icon (steps 2–4 below, for `<AGENTS_DIR>/agent-manager`), and replace `Icon style: not chosen yet` in its `memory.md` with the style number and name, so it can give later agents a matching icon.
2. Generate this agent's icon in `ICON_STYLE`, with a color and object that differ from the user's other agents.
3. Save it as `favicon.png` in the agent folder (see icons.md). If a `favicon.png` already exists, **ASK** before replacing it. Show the final icon in the chat once, the same way as the samples; don't show the generated images again in later messages.
4. Add the style and prompt to the agent's `memory.md`, commit `favicon.png` and `memory.md`, and push if the agent has a private GitHub backup.

T3 Code usually shows a new icon within a minute or two. If not, it appears after reloading or restarting T3 Code.

## Step 11: Check, try it, and choose the next workflow

Check each item below for this agent and fix what you can. Then **show the user the checklist itself**, one line per item: ✅ when it's done and checked, ⚠️ when it's skipped, failed or not checked yet, each ⚠️ with a few words on why. Leave out items that don't apply.

- The agent folder has `AGENTS.md`, `CLAUDE.md` (containing `@AGENTS.md`), and its memory files. No placeholders are left: search for `{{` (outside the agent manager's `.agents/` and `.claude/` folders, where they belong).
- The agent folder is its own Git repository with at least one commit, and `git -C "<TARGET>" status --porcelain` is empty.
- WordPress agent: `.mcp.json` and `.codex/config.toml` exist and are valid. With an inside connection: `git -C "<TARGET>" check-ignore .mcp.json .codex/config.toml` lists both, and the connection test in 7.3 passed.
- Codex users: the agent path is in `<CODEX_HOME>/config.toml` with `trust_level = "trusted"`.
- Email, or your own agent with connectors: whether the user connected them.
- The agent is a T3 project (checked with `t3_project_list`, or confirmed by the user).
- Icon, if made: `favicon.png` in the agent folder, committed.
- GitHub, if chosen: the agent has a private `origin`, and `git -C "<TARGET>" status -sb` doesn't show `ahead`.

In the same message, in `LANGUAGE`:

1. **How to use the agent:** click **New thread** (the pencil icon at the top of the sidebar) and choose the agent's project, then type a question. Always start agent work in that agent's own project, not in this setup thread.
2. A first prompt to try:
   - Workflow 1, email assistant: "Check first whether you can reach my mailbox. Then look at my unread emails from the past 3 days and suggest what I should do with each one."
   - Workflow 2, WordPress inside view: "Which 5 posts have I published most recently, and which categories do I use most?"
   - Workflow 3, WordPress outside view: "Check first which tools you have. Then analyse the public side of my site against The Website Specification. Give me a top 5 of improvements, ranked by impact and effort, and explain them for someone who isn't a developer."
   - Workflow 4, your own agent: the first prompt from step 6.4.
3. The agent remembers what they tell it about their preferences, and saves that in its own folder.

Then **ASK**: will they try the first prompt now (and come back to tell you whether the agent found its tools), set up another workflow, or stop here?

- **Try it now:** wait until they come back. If a tool is missing, help fix it: Codex needs the trusted folder (7.2), the mailbox and other connectors are connected in the browser (7.3), and new tools only appear in a new thread. Then ask whether they want another workflow or to stop.
- **Another workflow:** go back to step 5.
- **Stop:** send a short closing message that lists their agents. Remind them that to add another agent later, they start a new thread in the **Agent manager** project and describe what they need.
