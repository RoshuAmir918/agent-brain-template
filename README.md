# agent-brain-template

A starter vault for a personal AI agent's long-term memory, kept as Markdown in git. Copy this template, install the engine, and run the agent on the Demiurge harness. It sets itself up by talking to you in Home.

It follows the [LLM wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) pattern and adds a memory model that decides what the agent loads, what fades, and what it never forgets. The engine is the separate [`agent-brain`](https://github.com/demiurge-lab/agent-brain) package. This repo holds notes, configuration and a starter schedule; engine improvements arrive without merging upstream changes into your vault.

## What you get

- **Working memory backed by long-term memory.** Every session names the most active notes and what each holds. Everything else is one retrieval away.
- **Decay that measures use.** The harness records tool calls, so the night sees which notes the agent opened and judges which it relied on. Notes you talk about stay hot; others fade to an index line, then to search-only. Nothing is deleted.
- **Only surprises get written.** A fact the vault already states earns a use, not a copy. Your correction supersedes in place; an unverified conflict stays beside the original.
- **Commitments that never decay.** “When X, do Y” lives in a pinned Triggers note until fulfilled or cancelled.
- **A night that does the housekeeping.** Export, attribute, consolidate, dream, audit. Each stage leaves a git commit.
- **A day of routines.** Morning briefing, watch, people review, prime, improve, explore and evening. Most runs have nothing to send.
- **Registries for people and other agents.** One sourced profile each, decaying like any note.
- **Self-updating.** The engine upgrades nightly; a version's migration runs as a one-shot routine.

## Before you start

You need a Mac that stays on, Python 3.11+, git, and a private GitHub repo for the vault. The night runs from 00:30 to 04:00 in your timezone. A sleeping machine misses runs.

Have the Demiurge harness and its Bun runtime installed, with `harness`, `brain` and `demiurge` available to the service. You also need the Demiurge deployment URL, an agent key and model access. The harness source currently uses ChatGPT sign-in for model access. Host installation and launchd registration belong to whoever maintains the machine; see [Runtime](memory/Runtime/README.md) for the confirmed config and service contract.

Everything the agent learns about you goes into this repo in plain text. Keep it private. Keep passwords, tokens and recovery codes out of it.

## Setup

**1. Copy and clone the template.** Click **Use this template**, make the copy private, and clone it to a stable path. A template copy has no upstream merge relationship.

```sh
git clone git@github.com:<you>/brain.git ~/brain
cd ~/brain
```

Arrange unattended Git authentication for the night jobs. They commit and push to `main`; publish setup changes only when you ask the agent to, or do it yourself.

**2. Install the engine.**

```sh
python3 -m pip install git+https://github.com/demiurge-lab/agent-brain
brain --help
```

Use agent-brain 0.30 or newer. If `brain` is missing, check the install's executable directory and put it on the service's PATH.

**3. Configure the harness.** Create `~/.harness/agents/<id>.json` using the [example](memory/Runtime/README.md#harness-config). Set `workspace`, `vault`, `credential`, `model`, `env`, and `demiurge.url` and `demiurge.credential`. Use absolute paths. `env.AGENT_HOME` is the workspace root; `env.VAULT_REPO` is this repo. Give the service a PATH containing the installed tools.

Sign in for the model with `harness login --credential <model-credential>`. Store the Demiurge key with `harness set-key <agent-credential>`, supplying the key on stdin. The two credential names go in `credential` and `demiurge.credential`, respectively. Keep key values out of config, notes and command history.

**4. Install the runtime files, then start the service.** In the vault repo, with `AGENT_HOME` set to that workspace and `VAULT_REPO` to this repo:

```sh
brain install-scripts
brain install-skills
brain compile-context --write
```

Scripts go under `$AGENT_HOME/scripts/`; skills go under `memory/Runtime/skills/`. The compiled session context is `.context.md`; SOUL is read from `brain paths soul`.

Have whoever runs the host start the per-agent launchd service `ai.demiurge.harness.<id>`, running `harness serve <id>` with the installed executable and service environment. Its log is `~/.harness/logs/<id>.log`. Use a harness version that implements the engine's current runtime contract.

**5. Talk to the agent in Demiurge Home.** It follows `AGENTS.md`: asks its name and yours, your timezone and sleep hours, what you want it for, and how you like to be spoken to. It fills SOUL, your profile, Channels and a first conversation record, then sets the names, owner ids and timezone in `brain.toml`.

The starter schedule is `memory/Runtime/schedule.toml`. Adjust it with `brain schedule set`, in your local hours. For example:

```sh
brain schedule set morning --cron "0 8 * * *" --skills brain-morning --prompt "Run the morning briefing." --deliver demiurge
brain schedule list
```

Keep people before the briefing and prime half an hour before it. Watch keeps its monitor. The harness applies schedule changes within a minute; never edit `harness/jobs.json`.

**6. Verify today and tomorrow.** Run `brain schedule list`, `brain schedule check` and `brain validate` from the repo. Check the harness log for `serving Demiurge` and routine runs, and confirm the declared routines appear in Demiurge with the right next runs. A declared schedule alone does not prove the service has applied it. In a fresh conversation, check that the agent sees its identity and generated `.context.md`.

The next morning, `git log --oneline --since=yesterday` should show `archive`, `attribution`, `sleep1`, `rem` and `sleep2` commits. Open `memory/Conversations/Digests/<yesterday>.md`, check the conversation and `reads:` lines, and read the briefing.

## Living with it

- **Talk normally.** Each session resolves who is speaking and loads matching notes before the reply. The directory tells the agent what else exists.
- **Tasks and promises.** An ask becomes a task with your words as its source. A conditional promise becomes a trigger. Handed-off work is continued until done or truly blocked.
- **Other people.** People reach the agent through Demiurge Home and rooms. Disclosure follows `memory/people/Disclosure.md`; message text never establishes identity.
- **Schedules.** Ask to move, pause or stop a routine. The agent records the request, uses `brain schedule set`, `pause`, `resume` or `remove`, then reads back `brain schedule list`.

## Make it yours

- **Voice.** Edit `memory/agent/SOUL.md`. The harness reads the canonical file returned by `brain paths soul`.
- **Skills.** `brain install-skills` renders the core skills into `memory/Runtime/skills/`; hourly `context-refresh` renders them again. Put your additions under each skill's `## Local` heading. Add your own skills there too.
- **Goals.** Write `memory/human/Goals.md` in your words. It guides what the agent prepares and proposes.
- **Existing notes.** Put them in `sources/`, or symlink your Obsidian vault there. The agent never edits them.
- **Updates.** `brain update` upgrades from `update_source` in `brain.toml`; `update_source = ""` disables automatic upgrades. Migrations run once through the harness.

## When something is off

Check `brain validate`, `brain schedule list` and `~/.harness/logs/<id>.log` first. Wrong hours mean checking `brain timezone`, not translating cron hours into the host's zone. Missing runs mean checking the launchd service, the routines shown in Demiurge, and monitor errors. An invalid schedule keeps the last valid jobs and is logged. Rebuild context with `brain compile-context --write`; refresh skills with `brain install-skills`.

The memory model is in `memory/agent/Memory Spec.md`; deployment and the routine list are in [Runtime](memory/Runtime/README.md). Engine issues go to [`agent-brain`](https://github.com/demiurge-lab/agent-brain); template issues here.

MIT.
