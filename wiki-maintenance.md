---
name: Wiki Maintenance — Entity Isolation Rules
description: NCore, MSM, and Tamarind entity isolation; wikilink semantics for cross-entity references
type: reference
---

# Wiki Maintenance Rules

## Entity Isolation Principle (Critical)
NCore, Marky Sparky Media (MSM), and Tamarind are separate entities. Bernardo is the bridge between them but does NOT create business connections:
- **Bernardo as freelancer for MSM** — This is Bernardo's personal client relationship, NOT NCore having a client
- **Bernardo as co-founder of Tamarind** — Thiago owns Tamarind; Bernardo is not involved
- When writing about a person who works with multiple entities, describe each relationship separately without creating wikilinks between the unrelated entities

## Wikilink Semantics
- `[[X]]` creates a graph connection between two pages — this is deliberate and should be avoided when entities are unrelated
- If Entity A has a person who also works with Entity B, do NOT wikilink both entities from the same page unless there's a real business relationship
- Plain text mentions are fine; wikilinks create connections

## IG Profile Consolidation (Applied 2026-05-07)
Individual Instagram profile pages are noise and create artificial connections:
- `Beh Resende (IG)` → merged into Bernardo Resende.md, deleted standalone page
- `Empty Horizon (IG)` → merged into Mark Taynton.md, deleted standalone page
- `Léo Tarta (IG)` → merged into Léo Tarta.md, deleted standalone page
- `Thiago Vascon (IG)` → merged into Thiago Vascon.md, deleted standalone page
- Rule: IG details live in the person's main profile as a Digital Presence section, not separate pages

## Common Mistakes to Avoid
- **Wrong name:** "Tiago Taynton" is wrong; correct spelling is Mark Taynton
- **ALL CAPS filenames:** MARK_TAYNTON.md should be "Mark Taynton"
- **Underscore wikilinks:** `[[Mark_Taynton]]` should be [[Mark Taynton]]; Obsidian normalizes both but underscores look wrong
- **Profile sections:** "Relationship to NCore" on a non-NCore entity's page implies a business connection that may not exist
- **Freelancer framing:** "Bernardo works for MSM" is about Bernardo, not NCore's business

## File Naming
- Use spaces in filenames: "Mark Taynton.md", not "MARK_TAYNTON.md"
- Use proper case, never ALL CAPS
