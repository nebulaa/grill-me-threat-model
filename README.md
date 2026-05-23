# Grill Threat Model

A [Cursor Agent Skill](https://cursor.com/docs/context/skills) for repeatable, architecture-grounded threat modeling — inspired by [mattpocock/grill-with-docs](https://github.com/mattpocock/skills/tree/main/skills/engineering/grill-with-docs).

The workflow, phases, and decision model in this skill follow the process described in **[Threat Modeling: A Complete How-To Guide](https://nebulablogs.com/threat-modeling-a-complete-how-to-guide)**. Read that post for the full methodology; this repo encodes it as an agent-driven, resumable session.

## Install

**Project skill (recommended):** copy or symlink into your target repo:

```bash
mkdir -p /path/to/your-repo/.cursor/skills
cp -r .cursor/skills/grill-threat-model /path/to/your-repo/.cursor/skills/
```

**Personal skill:** from this repo root, install globally for all projects:

```bash
mkdir -p ~/.cursor/skills && cp -r .cursor/skills/grill-threat-model ~/.cursor/skills/
```

To stay in sync with this repo instead of copying, use a symlink:

```bash
mkdir -p ~/.cursor/skills && ln -sfn "$(pwd)/.cursor/skills/grill-threat-model" ~/.cursor/skills/grill-threat-model
```

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
5. Walk through threats one-by-one (critical → informational); separate **existing controls** from **recommended actions**
6. Record avoid / mitigate / accept / transfer as a **decision** — do not mark implemented until code and architecture docs are updated
7. Persist session state in `threat-model/THREAT-MODEL.md` for pause/resume
8. Bump version when architecture changes invalidate >25% of threats

## Output layout

```
threat-model/
├── THREAT-MODEL.md      # Index, session, summary
├── system-model.md      # DFD, assets, boundaries
└── threats/
    └── TM-001-slug.md
```

Formats are defined in `.cursor/skills/grill-threat-model/THREAT-MODEL-FORMAT.md` and `THREAT-ENTRY-FORMAT.md`. For the underlying process (scope, system model, STRIDE, risk treatment), see the [how-to guide](https://nebulablogs.com/threat-modeling-a-complete-how-to-guide).

## Skill files

| File | Role |
|------|------|
| `SKILL.md` | Workflow and session rules |
| `THREAT-MODEL-FORMAT.md` | `threat-model/` directory spec |
| `THREAT-ENTRY-FORMAT.md` | Per-threat markdown template |
| `FRAMEWORKS-REFERENCE.md` | STRIDE, DREAD, CVSS, ATT&CK, mitigations |

## License

Use and adapt freely in your projects.
