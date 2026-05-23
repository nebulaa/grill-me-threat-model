# Grill Threat Model

A [Cursor Agent Skill](https://cursor.com/docs/context/skills) for repeatable, architecture-grounded threat modeling — inspired by [mattpocock/grill-with-docs](https://github.com/mattpocock/skills/tree/main/skills/engineering/grill-with-docs).

## Install

**Project skill (recommended):** copy or symlink into your target repo:

```bash
mkdir -p /path/to/your-repo/.cursor/skills
cp -r .cursor/skills/grill-threat-model /path/to/your-repo/.cursor/skills/
```

**Personal skill:** copy to `~/.cursor/skills/grill-threat-model/`.

## Use

In Cursor, invoke the skill by name or ask explicitly, for example:

- “Run grill-threat-model on this repo”
- “Threat model the API using our architecture docs”
- “Resume the threat model from where we paused”

The agent will:

1. Read architecture docs and code
2. Confirm scope, assumptions, and risk profile
3. Build `threat-model/system-model.md` (assets, boundaries, flows, DFD)
4. Enumerate threats (STRIDE) under `threat-model/threats/`
5. Walk through threats one-by-one (critical → informational) and record avoid / mitigate / accept / transfer
6. Persist session state in `threat-model/THREAT-MODEL.md` for pause/resume
7. Bump version when architecture changes invalidate >25% of threats

## Output layout

```
threat-model/
├── THREAT-MODEL.md      # Index, session, summary
├── system-model.md      # DFD, assets, boundaries
└── threats/
    └── TM-001-slug.md
```

Formats are defined in `.cursor/skills/grill-threat-model/THREAT-MODEL-FORMAT.md` and `THREAT-ENTRY-FORMAT.md`.

## Skill files

| File | Role |
|------|------|
| `SKILL.md` | Workflow and session rules |
| `THREAT-MODEL-FORMAT.md` | `threat-model/` directory spec |
| `THREAT-ENTRY-FORMAT.md` | Per-threat markdown template |
| `FRAMEWORKS-REFERENCE.md` | STRIDE, DREAD, CVSS, ATT&CK, mitigations |

## License

Use and adapt freely in your projects.
