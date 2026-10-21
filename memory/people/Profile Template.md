---
kind: orientation
pinned: false
tier: warm
uses:
- '2026-10-03'
uses_inferred: []
activation: -2.89
---
# Person profile template

Copy this into `People/Verified_Name/Profile.md` and fill it with sourced facts. Remove unused sections. Do not invent answers. Follow [[memory/people/README|People guidance]].

## Identity and relationship

- **Name and aliases:** verified spelling, source, date.
- **Identity status:** established, partial or unresolved, with the platform the account belongs to.
- **Relationship to the owner:** what is known, who stated it, date, confidence. Shared surnames do not establish kinship.
- **Shared activities or projects:** brief, linked to the owning project notes.

## Identifiers

Every address, number or platform id that means this person, one per line, each with the date and source it was confirmed. The engine resolves who is speaking by matching these against the session, so an identifier that is not here cannot be matched.

- Slack: U0XXXXXXX (confirmed <date>, <source>)
- Email: name@example.com (confirmed <date>, <source>)
- Phone: +1 555 000 0000 (confirmed <date>, <source>)

## Contact details

For each route: channel and exact address or account ID; who supplied it (the owner, the person's account, or message headers); date and last check; the owner's account in that exchange, if known; preferred channel or style only when supported. A listed email is not proof of deliverability.

## Useful context

Dated, attributed facts that help the owner with this relationship. Keep the owner's statements, the person's self-report, observed events and inferences separate.

## History

The Profile stays under 300 words and holds only what is stable. Every dated interaction, claim and request goes in `History.md` beside this file, newest first, one line each: `- YYYY-MM-DD, <channel>: <what they said or asked>; <how it went>.` Open commitments link to `agent/Tasks.md` or the owning hub. End the Profile with a wikilink to that History note.

## Relevant unknowns

Only questions that matter to a real next step.

Return to [[memory/people/README|People]].
