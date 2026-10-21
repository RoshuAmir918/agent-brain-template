# Agent instructions

This repository is a long-term memory for a personal AI agent. If you are an agent reading this, you are either the agent that owns it or a guest. Either way, read `memory/START.md` first and follow its rules; `sources/` is read-only; write only under `memory/`.

## If this brain is not set up yet

You will know because `memory/agent/SOUL.md` still has bracketed placeholders and `memory/agent/Tasks.md` has only T-001. Set it up now, with the owner present. Do these in order and say what you are doing as you go. Two things are interactive and the owner must run them if they have not: `hermes setup` (provider and key) and `hermes gateway setup` (a messaging platform). Check `hermes doctor` first; if it reports no provider, stop and ask the owner to run `hermes setup`.

1. **Ask the owner, in a conversation, not a form:** what they want to call you; their name; their timezone and roughly when they wake and sleep; the channel they will mostly talk to you on; what they want you for, in their words; and two or three things about how they like to be spoken to. Keep their answers verbatim where they are specific.
2. **Write what you learned:** fill the bracketed parts of `memory/agent/SOUL.md` and `memory/human/Profile.md`; write one dated record `memory/Conversations/<today> First conversation.md` quoting their answers; set the main channel row in `memory/agent/Channels.md`.
3. **Set names and environment:** put the owner's name and your name in `brain.toml` (`human_name`, `agent_name`). The cron scripts assume this repo is at `~/brain`; if it is elsewhere, `VAULT_REPO` must be set in the environment Hermes runs from, and `HERMES_HOME` if it is not `~/.hermes`. Confirm `git push` works from this machine without a prompt; if it does not, stop and tell the owner, because the nightly jobs push unattended.
4. **Install:** `python3 -m pip install git+https://github.com/RoshuAmir918/agent-brain` if `brain --help` does not work yet; `brain install-scripts` (puts the two cron scripts in the Hermes scripts dir); `brain install-skills` (renders the skills into `memory/hermes/skills/`, and prints the path for `skills.external_dirs`); `brain install-provider` then `hermes config set memory.provider brain` (the provider puts who-you-are-talking-to and matching notes into every session); the owner's platform user ids into `owner_user_ids` in `brain.toml` (for Slack the `U...` id; ask, never guess); then the config keys listed in `memory/hermes/README.md`: `skills.external_dirs`, `sessions.auto_prune` false, `sessions.max_export_messages` raised, `memory.write_approval` false, `skills.write_approval` true, and the platform home channel set to the owner's DM if a platform is connected. Tell the owner each key you set.
5. **Generate:** `brain nightly --write` once from the repo root, then run `~/.hermes/scripts/refresh.sh`. That writes the real `.hermes.md` over this bootstrap and copies SOUL into the Hermes home.
6. **Schedule:** create the cron jobs from `memory/hermes/README.md`, adjusting hours to the owner's sleep. Create one, check `hermes cron list` shows the next run in the owner's timezone, then the rest.
7. **Verify** before you finish: `hermes skills list` shows the `brain-*` skills, `hermes cron list` shows every job with a next run in the owner's timezone, and `.hermes.md` now begins with "Generated nightly by `brain`".
8. **Commit** everything you wrote with a message like `setup: first conversation and identity`, push, and tell the owner what will happen tonight (the five night stages and their times) and what to check in the morning (the night commits, the digest, the briefing).

Ask before running anything that needs their credentials or sends a message anywhere. Do not invent facts about the owner to fill a section; leave it as "unknown" and ask when it matters.

## Once it is set up

Use the `brain-recall` skill for retrieval and small daytime writes. The night routines own structure. Never edit `sources/`, SOUL, the skills, Triggers' rules or the Memory Spec from a routine; those change only when the owner asks.
