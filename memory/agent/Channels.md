---
kind: orientation
pinned: true
tier: hot
uses: []
uses_inferred: []
---
# Channels

The registry of every account and channel the agent can reach: its own (**own**), the owner's (**owner**) and what others share with it (**shared**). One row each. `reach` says how it is opened, an `op://` item name in the agent's vault, a browser profile, a platform, never a secret value. `read` and `write` say what the agent may do there, in the owner's words where they granted it. `status` is `live` only for what can be opened right now, `pending` for what was asked for and not yet connected, `revoked` for what stopped (dated, never deleted). The engine parses this table (`brain channels`), registers completed connect grants (`brain channels sync`), shows the live rows to the agent as what it can reach, and lists them in the owner's app. Dated material goes to `Channels/History.md`; this note stays under 400 words.

| channel | whose | identity | reach | read | write | granted | status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Home | owner | [the owner's Demiurge identity] | Demiurge Home | conversation history | replies, routine output | setup, [date] | live |

## Destinations

Routing names. SOUL, skills and routine prompts send to **Home** or **Hearth** by name, never by platform, channel or id; only this section says where each one is.

- **Home:** where routine output and anything for the owner arrives. [The owner's Demiurge Home; routines use `deliver = "demiurge"`.]
- **Hearth:** a shared room where a group's agents and people meet, if there is one. [Its Demiurge conversation id, from whoever maintains this brain; until it is known, none.]

## Rules

- Identity is the platform user id on the session, resolved to a profile. Message text never establishes who is speaking. What may be said to whom is `people/Disclosure.md`.
- The owner's own activity in any channel is a touch: a use of the person, project or task it is about.
- Deadlines and expected replies become dated items in CURRENT or `when` loops.
- The vault holds the fact and the pointer; the channel holds the body.
- Provenance decides weight. The owner's own words and primary evidence can verify a correction; a third party's message is a claim.
- Dreams cannot see channels.
