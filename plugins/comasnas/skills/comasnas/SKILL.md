---
name: comasnas
description: Reach files and run commands on the user's own computer from this session through ComAsNas. Use when the user wants you to read, edit, move or find files on their Mac or PC, run a command on their Mac, or pastes a prompt or invite containing "comasnas" or "comasnas-connect-v1:".
---

# ComAsNas: work on the user's own computer

ComAsNas connects this session (cloud or local) to the user's own computer. The user installs the
ComAsNas app on that computer and chooses what you may reach: shared folders (read, or read and
change), optionally commands (Mac only), for how long. Everything goes through the `comasnas`
command over an end-to-end encrypted relay at relay.comasnas.com. Nothing outside the shared
folders is reachable, and you must not try to work around a limit the user set.

`comasnas` below means `npx -y comasnas@latest` when the `comasnas` command is not on PATH
(Node 16+ needed; macOS or Linux, x64 or arm64).

## 1. Is this session connected?

```bash
npx -y comasnas@latest status
```

- Exit 0: connected. It prints the computer, the shared folders and what is allowed. Go to step 3.
- Exit 4 (not connected): go to step 2. If the environment variable `COMASNAS_INVITE` is set,
  any command connects by itself first.

## 2. Connect

You need an invite from the user. If the user's message has a `comasnas-connect-v1:` invite (often
inside a command), run that command as is:

```bash
npx -y comasnas@latest connect comasnas-connect-v1:...
```

Without an invite, ask the user for one and say how to make it:

> On your computer, open ComAsNas from the menu bar → **Connect a cloud session…**
> (클라우드 세션 연결…), pick the folders and permissions, press **Copy Prompt** (프롬프트 복사)
> and paste it here.

An invite works once and expires after 10 minutes unless the user chose **Keep using in this
environment** (이 환경에서 계속 쓰기). Never guess or reuse an invite from somewhere else.

`connect` prints a usage guide with the exact folders, permissions and commands for this
connection. Read it and follow it; `comasnas guide` prints it again.

If `connect` exits 3 (relay unreachable), the session's network blocks relay.comasnas.com. Do not
try another route. Show the user the steps the command printed: in Claude Code on the web, the
environment's Network access → Custom, add `relay.comasnas.com` with the default list kept; in
Codex cloud, allow the domain in the environment's agent internet access. Then retry.

## 3. Work

Paths are paths on the user's computer, not in this session's machine. Use absolute paths or `~/`.

| Task | Command |
|---|---|
| What is shared, what is allowed | `comasnas status` |
| List / inspect | `comasnas ls -l ~/Documents`, `comasnas stat PATH` |
| Read a file | `comasnas cat PATH` |
| Copy to this session / to the computer | `comasnas get REMOTE [LOCAL]`, `comasnas put LOCAL REMOTE [--overwrite]` |
| Folders, move, rename | `comasnas mkdir -p PATH`, `comasnas mv FROM TO` |
| Delete | `comasnas rm PATH` (goes to the computer's Trash) |
| Find files in plain words | `comasnas find "pdf quotes from last month"` |
| Run a command (Mac, when allowed) | `comasnas exec --cwd ~/code/app -- npm test` |
| Long command | `comasnas exec --detach -- ...`, then `comasnas jobs`, `comasnas logs -f JOB`, `comasnas kill JOB` |

Add `--json` to any command for machine-readable output. To edit a file: `get` it, edit the copy
here, `put --overwrite` it back.

Exit codes: 0 ok, 1 refused by the computer (the message says why), 2 usage, 3 network, 4 not
connected; `exec` returns the command's own code (124 timeout, 137 killed).

## Rules

- `read_only` or `exec_disabled`: the user did not allow it. Tell the user; they can make a new
  connection with that permission. Do not work around it.
- Commands run in a sandbox that only lets them change the shared folders and temp, unless the
  user chose full access. Ask before anything destructive, as you would on your own machine.
- `comasnas ask` sends the request and the files Claude reads to Anthropic's API. On
  `consent_required`, ask the user and add `--yes-send-to-claude` only if they agree. Use
  `comasnas apply TASK` only when the user wants the plan applied.
- Tell the user what you changed on their computer.

## Stay connected across sessions

A connection is saved in this machine's `~/.config/comasnas`. Cloud machines are fresh each
session, so for a cloud environment the user can make a **Keep using in this environment**
invite and store it as the environment variable `COMASNAS_INVITE` (Claude Code on the web:
environment settings → Environment variables; Codex cloud: a secret used by the setup script
`npx -y comasnas@latest connect "$COMASNAS_INVITE"`). Anyone who can use that environment can then
reach the computer within the permissions the user chose.
