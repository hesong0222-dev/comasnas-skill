# ComAsNas skill

Let an AI session (Claude Code, Codex, in the cloud or on your machine) reach the files on your own
computer and run commands there, within the folders and permissions you choose.

You need the ComAsNas app on the computer you want to reach: <https://comasnas.com>. The session
uses the `comasnas` command (`npx -y comasnas@latest`, Node 16+, macOS or Linux).

## Install

**Claude Code**

```
/plugin marketplace add hesong0222-dev/comasnas-skill
/plugin install comasnas@comasnas
```

**Codex**: copy the folder `plugins/comasnas/skills/comasnas` to `~/.agents/skills/comasnas`
(or run `npx -y comasnas@latest skill --user`, which does that for Claude Code and Codex).

**Every cloud session of a repository** (Claude Code on the web, Codex cloud): in the repository, run

```
npx -y comasnas@latest skill
```

and commit the `.claude/skills/comasnas` and `.agents/skills/comasnas` folders it creates.
`npx -y comasnas@latest skill --user` installs it in your home folder instead.

## Use

Ask the session for something on your computer ("summarize the PDFs in ~/Documents/Contracts",
"run the tests in ~/code/app on my Mac"). The first time, it asks you for a connection: in the
ComAsNas menu choose **Connect a cloud session…**, pick folders and permissions, press
**Copy Prompt** and paste it into the session. A cloud environment must allow the domain
`relay.comasnas.com`; the session tells you how when it is blocked.

What the session can do is set on your computer, not by the skill: read only or read and change
the shared folders, commands off, sandboxed or full access (Mac only), and for how long. Deleted
files go to the computer's Trash. You can disconnect a session from the ComAsNas menu at any time.
