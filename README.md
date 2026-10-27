# agent-brain-template

A starter vault for a personal AI agent's long-term memory, kept as Markdown in git. Copy this template, install the engine, start [Hermes Agent](https://github.com/nousresearch/hermes-agent) inside your copy, and the agent sets itself up by talking to you.

It follows the [LLM wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern (your sources, an agent-maintained wiki, a schema file, an index, a log) and adds what that pattern lacks: a memory model that decides what the agent loads, what fades, and what it never forgets. The engine is the separate [`agent-brain`](https://github.com/RoshuAmir918/agent-brain) package. This repo holds only notes and one config file, so your copy never has to merge anything from upstream, and engine improvements reach you on their own.

## What you get

- **Working memory backed by long-term memory.** A small hot set is in every session. Everything else is one retrieval away, and the agent is told what exists so it knows to look.
- **Decay that measures use, not loading.** Every note has an activation score computed nightly from when the agent relied on it. Notes you talk about stay hot; notes you don't fade to an index line, then to search-only. Nothing is deleted.
- **Attribution that watches, not asks.** Hermes records every tool call, so the night sees which notes the agent opened before each reply and judges which it relied on. The agent never has to cite.
- **Only surprises get written.** A fact the vault already states earns a use, not a copy. A correction from you supersedes in place. A conflicting third-party claim is kept beside the original and raised in the morning.
- **Commitments that never decay.** "When X, do Y" lives in a pinned Triggers note and fires when the world matches it, however old.
- **A night that does the housekeeping.** Export, attribute, consolidate, dream, audit. Each stage is one git commit, so `git log` tells you whether it ran and `git revert` undoes it.
- **A day of routines you can keep or delete.** Morning briefing, hourly watch, midday and evening register checks, a daily people review, and a proactive explore pass that picks one thread from your recent conversations and pushes it forward.
- **Registries for people and for your other agents.** One sourced profile each, decaying like any note.
- **Self-updating.** The engine upgrades itself every night. When an upgrade needs something changed in your notes, it leaves a prompt and your agent carries it out.

## Before you start

You need:

- A Mac or Linux machine that stays on. The night runs at 00:30 to 04:00 local time; a Mac mini, a home server or a small VPS all work. A laptop that sleeps will miss nights.
- Python 3.11 or newer and git.
- A GitHub account. Your vault is a git repo and the nightly jobs push to it.
- Hermes Agent installed and set up with a model provider. If you have not done that yet:
  ```sh
  curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
  hermes setup          # provider and API key; interactive
  ```
  If you want to talk to your agent from Slack, Telegram or Discord, also run `hermes gateway setup` and connect the platform. You can add this later; the vault works from the terminal alone.

Everything the agent learns about you goes into this repo, in plain text. Keep it private. Never put passwords, tokens or recovery codes in it; the agent is told never to read them, but the safer rule is that they are never there.

## Setup

**1. Copy the template.** Click **Use this template** at the top of this page, name the repo (for example `brain`), and make it **private**. Do not fork or clone this repo directly; a template copy has no link back here, which is what you want, since the engine updates through a different path.

**2. Clone it to `~/brain`.** That exact path is what the cron scripts assume, so use it unless you also want to set `VAULT_REPO` in the environment Hermes runs from.

```sh
git clone git@github.com:<you>/brain.git ~/brain && cd ~/brain
```

Make sure `git push` works from this machine without a prompt (an SSH key, or `gh auth login`). The nightly jobs push unattended.

**3. Install the engine.**

```sh
python3 -m pip install git+https://github.com/RoshuAmir918/agent-brain
brain --help
```

If `brain` is not found afterwards, pip put it somewhere that is not on your PATH; `python3 -m pip show -f agent-brain | grep bin` shows where, or install with `pipx` instead.

**4. Start Hermes from the repo root and let the agent set itself up.**

```sh
cd ~/brain && hermes
```

Hermes reads `.hermes.md` from the directory it starts in. On a fresh copy that file tells the agent it is looking at an unset-up brain and points it at `AGENTS.md`, which holds the setup procedure. The agent will:

- ask you what to call it, your name, your timezone and sleep hours, which channel you will mostly use, what you want it for, and how you like to be spoken to. Answer in your own words; your answers become its identity file, its profile of you, and its first memory;
- write those into `memory/agent/SOUL.md`, `memory/human/Profile.md` and a dated conversation record, and put the two names in `brain.toml`;
- run `brain install-scripts` and `brain install-skills`, and set the Hermes config keys it needs (it will tell you which, and ask before anything that touches credentials);
- generate the real `.hermes.md`, copy SOUL into the Hermes home, and create the cron jobs, checking that the first one lands in your timezone;
- commit, and tell you what will happen tonight.

Say yes when it asks to run commands. If it asks you to run something yourself, it is because that command is interactive. The whole thing takes about ten minutes.

**5. Verify, the same day.** `hermes skills list` shows skills named `brain-*`. `hermes cron list` shows the jobs with next-run times in your timezone. A new Hermes session can quote the first lines of `.hermes.md` when you ask what its project context says.

**6. Verify, the next morning.** From `~/brain`:

```sh
git log --oneline --since=yesterday
```

You should see `archive`, `attribution`, `sleep1`, `rem` and `sleep2` commits. Open `memory/Conversations/Digests/<yesterday>.md` and check it holds your conversation, with `reads:` lines under the agent's turns. Then read the briefing it sent you.

## Living with it

- **It knows who it is talking to.** Every session starts with the platform user id resolved to a profile: your friend's profile and the rules for what they may be told are in the prompt before the first reply, and a stranger is treated as one. Before each turn it also sees which notes match the message, so looking something up is a click, not a decision.
- **Talk to it normally.** It looks things up before answering when a name, a project or a past decision is involved; the directory at the end of its session context tells it what exists. If it says it searched and found nothing, it means that.
- **The morning briefing** is the one message a day it always sends: what moved, what it needs from you, what it noticed overnight, whether the night ran. Everything non-urgent waits for it.
- **Tasks and promises.** Say "remind me when X" or "when the deploy lands, tell Y" and it becomes a trigger that never decays. Ask it for something and it becomes a task with your words as the source.
- **People.** Mention someone and a profile starts; the daily people review keeps it sourced and revisable. The agent forms its own impressions and says so.
- **Other people.** Anyone on your platform can message it. It resolves who is asking from the platform id, tells them nothing about you or anyone else unless a row in `memory/people/Disclosure.md` allows it, and logs what it declined for your morning briefing. Say "you can always share my schedule with close friends" and it writes the rule, scoped to the group, before it ever applies it.
- **Other agents.** Tell it about another agent you run and it registers a profile: what the agent is for, how to reach it, what its reports are worth.
- **Schedules.** Ask for a routine to move, pause or stop; it records the request, updates the table in `memory/hermes/README.md`, and applies it. It never changes a schedule on its own.

## Updating

Nothing to do. The nightly export job runs `brain update` first, which upgrades the engine from `update_source` in `brain.toml` and reinstalls the scripts; the hourly refresh re-renders the core skills and the session context. If an upgrade needs a change to your notes, the agent finds a migration prompt the next morning and tells you before doing it. To pin a version, set `update_source = ""`.

## Make it yours

- **Voice.** Edit `memory/agent/SOUL.md`. The refresh script copies it into Hermes within the hour.
- **Routines come in two kinds.** Attribute, Sleep 1, Sleep 2, REM, Improve, People, Explore, Watch and recall are the engine's: it re-renders them on every refresh, keeping a `## Local` section at the end of each where your own additions go. To change the rest of one, fork `agent-brain`, change it there, and point `update_source` at your fork. Morning, midday and evening are defaults: written once on install, then yours. Edit them in `memory/hermes/skills/`, or delete the folder and its cron line. Add your own skill the same way.
- **Your goals.** `memory/human/Goals.md`, in your words. The agent uses it to choose what to work on and what to propose, and never to nag.
- **Your existing notes.** Put them in `sources/`, or symlink your Obsidian vault there. The agent reads them and never edits them.
- **Obsidian.** Open the repo as a vault. The wikilinks and graph work as-is.
- **Folder names.** `brain.toml` maps the engine to this layout. You can rename the folders if you update it, but there is no reason to.

## When something is off

- **`pip install` fails with a permissions or not-found error.** The engine repo may still be private; ask whoever shared this with you, or install from a fork you can reach.
- **The agent did not see the bootstrap.** Hermes was not started from the repo root. `cd ~/brain && hermes`.
- **Cron jobs run at the wrong hour.** The Hermes docs do not say which timezone a cron expression uses. `hermes cron list` shows the next run; if it is off, ask the agent to shift the hours.
- **No night commits.** Check the gateway is running (`hermes` must be running for agent cron jobs to execute) and `hermes cron list` shows the jobs enabled. The morning briefing also reports a missing stage.
- **The digest is empty or has no `reads:` lines.** The session-export parser's field-name assumptions differ from your Hermes version. They are listed in the engine's `docs/engine.md`; open an issue on `agent-brain` with a redacted sample of `memory/Conversations/Sessions/all.jsonl`.
- **SOUL changes are not showing up.** The hourly refresh copies it; wait for the next run, or run `~/.hermes/scripts/refresh.sh` yourself.
- **You want to start over.** It is a git repo. `git log` shows every night; `git revert` undoes one.

## The model in one paragraph

Every note carries a tier (hot, warm, cold), a list of dates it was relied on, and an activation score. Activation is ACT-R base-level activation with exponent 1, calibrated so one use stays hot about a week and warm about a month, and daily use for a month stays hot most of a year. Hot notes load every session, warm notes are listed by one line in an index, cold notes are search-only. Retrieval spreads one hop over wikilinks. Writes go through a surprise gate. Open commitments and pinned notes never decay. The engine does the arithmetic and refuses to commit if the vault is inconsistent; the agent does the judgment and its judgment is audited the next night. See `memory/agent/Memory Spec.md`.

## Status

Built in October 2026 for one person's assistant and generalised. Engine issues and pull requests go to [`agent-brain`](https://github.com/RoshuAmir918/agent-brain); template issues here.

MIT.
