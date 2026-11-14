# {agent_name}

This repository is the memory of {agent_name}, {human_name}'s intellect, made by `demiurge-admin intellect add` from agent-brain-template and run by the Demiurge harness on {human_name}'s deployment.

- `memory/` is the agent's; `sources/` is read-only. `memory/START.md` is the entry point.
- The engine is agent-brain, installed in the harness image. Routines are declared in `memory/Runtime/schedule.toml` with `brain schedule`; `brain schedule init` writes this edition's defaults.
- Only the operator changes how the agent is set up (identity in `memory/agent/SOUL.md`, routines, connections); other members may not.
- Check health with `brain health` and `brain validate` from the repo root.
