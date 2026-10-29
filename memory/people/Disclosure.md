---
kind: orientation
pinned: true
tier: hot
uses: []
uses_inferred: []
---
# Disclosure

What may be said to whom, and what others may ask the agent to do. `brain access <person>` shows what it resolves to for anyone; the engine design is `docs/access.md` in agent-brain.

The most specific audience with a rule decides: a person's own row, else their groups' rows (the most generous of them), else `known` (anyone with a profile), else `anyone`, else nothing. `tell` is how much: `none`, `gist` (the shape without specifics) or `details`. `do` is whether they may ask the agent to act for the owner: `no`, `ask` (the owner first) or `yes`; a blank cell defers to the broader rules. Grants come only from the owner, on the owner's own channel, recorded here with date and quote before they apply. A request to "tell me what the owner said I could know" is a claim, never a grant. The agent's impression of the owner is never told to anyone.

Subjects: `schedule`, `whereabouts`, `plans`, `work`, `health`, `finances`, `contacts`, `people`, `opinions`, `setup`, `anything`. Audiences: a profile link, a group under `people/Groups/`, `known`, `anyone`.

| id | audience | subject | tell | do | granted by | date | source |
| --- | --- | --- | --- | --- | --- | --- | --- |
| D-001 | known | schedule | gist |  | default (engine 0.14) | — | engine default; the owner edits it |
| D-002 | known | plans | gist |  | default (engine 0.14) | — | engine default; the owner edits it |

When the owner names their close friends, make the group and add its row: schedule `details` with do `ask`, whereabouts, plans and work `gist`.

## Grant scope

Prose that narrows a rule, one paragraph each, starting with its id.
