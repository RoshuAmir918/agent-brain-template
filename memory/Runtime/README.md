---
kind: orientation
pinned: true
tier: hot
uses: []
uses_inferred: []
---
# Runtime

The Demiurge harness runs the agent; agent-brain owns its memory and declared routines. The memory model is in [[memory/agent/Memory Spec|Memory Spec]].

## Harness config

Create `~/.harness/agents/<id>.json`. Agent ids use lowercase letters, digits and hyphens, starting with a letter or digit. Replace placeholders with real values; paths are absolute, not `~` expressions.

```json
{
  "id": "agent",
  "workspace": "/absolute/path/to/agent",
  "vault": "/absolute/path/to/brain",
  "credential": "model",
  "model": "<model>",
  "env": {
    "AGENT_HOME": "/absolute/path/to/agent",
    "VAULT_REPO": "/absolute/path/to/brain",
    "PATH": "<directories containing brain, demiurge, harness and bun>:/usr/bin:/bin"
  },
  "demiurge": {
    "url": "<Demiurge deployment URL>",
    "credential": "agent"
  }
}
```

`credential` names model access: `harness login --credential model` signs in through the device flow. `demiurge.credential` names the agent key: supply it on stdin to `harness set-key agent`. Keys belong in the harness credential store, never in this JSON or the vault. Ask the owner before credential work. The source also supports optional `effort`, `serviceTier`, `skillsDirs`, `demiurge.binary` and `demiurge.indexDirs`; omit them unless needed.

`workspace` is `AGENT_HOME`, holding `records/`, `scripts/` and runtime state under `harness/`. `vault` is `VAULT_REPO`, this git repo, and the agent's working directory. Set `AGENT_HOME` explicitly in `env`. The service's PATH must find the installed tools; shell startup files are not its configuration.

## Install and serve

1. Install agent-brain 0.30+ and check `brain --help`. Fill the names, permanent owner ids and owner's IANA timezone in `brain.toml`; `brain timezone` reads it.
2. From the repo, with `AGENT_HOME` and `VAULT_REPO` set, run `brain install-scripts` and `brain install-skills`. Scripts go into `$AGENT_HOME/scripts/`; core skills into `memory/Runtime/skills/`.
3. Run `brain compile-context --write`. `.context.md` is the compiled session context; `brain paths soul` gives the canonical SOUL file the harness reads.
4. Write the routines with `brain schedule init`, then check them with `brain schedule list` and `brain schedule check`.
5. Have whoever runs the host register/start launchd service `ai.demiurge.harness.<id>` running `harness serve <id>`, with the installed executable, vault working directory and service PATH. Its log is `~/.harness/logs/<id>.log`. The source tree does not include a harness service installer; use the host's service setup rather than inventing a command.
6. Talk in Demiurge Home and complete `AGENTS.md`: fill identity and profile, set the owner's timezone, adjust routines with `brain schedule set`, then run `brain nightly --write` and `brain compile-context --write`. Read back `brain schedule list` and run `brain validate`. Check the log for `serving Demiurge`, routine starts, outcomes and errors; confirm next runs in Demiurge. Verify identity and `.context.md` in a fresh conversation. Use a harness build implementing the current engine runtime contract.

## Schedule

`memory/Runtime/schedule.toml` is the source of truth, not `$AGENT_HOME/harness/jobs.json`. `brain schedule init` writes the engine's default routines for this vault's edition; `brain schedule list` shows what is declared. Change routines only with `brain schedule`; the harness applies changes within a minute. Cron hours are the owner's local hours (`brain timezone`).

```sh
brain schedule set watch --cron "*/15 6-22 * * *" --skills brain-watch --deliver demiurge --monitor watch-signal.sh
brain schedule list
brain schedule check
```

Use `pause`, `resume` or `remove` to stop or restore a routine. `set` also accepts `--every <n>m` / `<n>h` or `--at <ISO datetime with offset>` instead of `--cron`. Delivery is `local` (no message), `demiurge` (Home), or `demiurge:<conversation id>`. Runs with nothing to say end with `[SILENT]`. Invalid declarations keep the last valid jobs and are logged.

Record a schedule change request before applying it. The owner can change anything; routines the owner asked for by name are the owner's, added with `--owner`.

Watch and explore are gated by `watch-signal.sh` and `explore-signal.sh`, and the evening by `evening-signal.sh`: a model run happens only when the signal has something new; a failed monitor is an error. `brain install-scripts` installs them. Recall governs conversations; setup handles requested changes. Core skills are re-rendered hourly; put this vault's additions under `## Local`.

## Export, refresh and verification

`session-export` runs `brain update`, checks migrations, then `brain export-sessions` and `brain digest`. Conversation Markdown goes into `memory/Conversations/Sessions/`, digests into `memory/Conversations/Digests/`, and JSON shards and routine transcripts under `$AGENT_HOME/records/sessions/`. The script redacts exports, commits `archive(<date>)`, pushes and calls `refresh.sh`.

`context-refresh` runs `refresh.sh`: pull, sync people, compile `.context.md`, render skills, write health, and refresh the workspace's SOUL copy. It commits and pushes changed context or runtime files. The harness reads canonical SOUL directly. Version migrations run once as routines.

After a night, `git log --oneline --since=yesterday` should show `archive`, `attribution` and `sleep1`, plus `context` when needed. `brain health` reports missing stages. Check the digest's turns and `reads:` lines. For missing runs, inspect `brain schedule list`, `brain validate`, the routines in Demiurge and `~/.harness/logs/<id>.log`; never repair the runtime's job store by hand.
