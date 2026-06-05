[![skills.sh](https://skills.sh/b/cloudomate/skills)](https://skills.sh/cloudomate/skills)

# Cloudomate Skills

Skills are folders of instructions, scripts, and resources that an agent loads
dynamically to improve performance on specialized tasks. This repository is the
org-wide home for [Cloudomate](https://github.com/cloudomate)'s agent skills —
used by the [Hermes Agent](https://github.com/cloudomate) and the **Iva**
on-device voice assistant.

Each skill is self-contained in its own folder under [`skills/`](./skills) with a
`SKILL.md` file holding the YAML frontmatter (`name`, `description`, …) and the
instructions the agent reads. The `description` is what the agent uses to decide
*when* to load a skill, so write it around the user phrases that should trigger
it.

> Layout follows the [anthropics/skills](https://github.com/anthropics/skills)
> convention so it works with the Skills Hub, the Claude Code plugin
> marketplace, and `skills.sh`.

## Repository layout

```
skills/                 # one folder per skill: skills/<name>/SKILL.md
  iva-hermes/           #   umbrella: what Iva is + its device capabilities
  volume-control/       #   change/persist speaker volume on the device
template/SKILL.md       # starting point for a new skill
.claude-plugin/         # marketplace.json — Claude Code plugin definition
skills.sh.json          # skills.sh category groupings for the tap
```

Skills are kept **flat** (`skills/<name>/SKILL.md`, never nested) so the tap
enumerator and the plugin marketplace can discover them. Categories come from
`skills.sh.json`.

## Available skills

| Skill | What it does |
|---|---|
| [`iva-hermes`](./skills/iva-hermes) | Umbrella description of the Iva on-device voice assistant and the device-control skills it exposes. |
| [`volume-control`](./skills/volume-control) | Change / set / mute the device speaker volume via the persisted `iva-volume` helper (survives reboot). |

## Using these skills

### Skills Hub (tap)

```bash
# browse / install / update via the Skills Hub:
hermes skills tap add cloudomate/skills
hermes skills install cloudomate/skills/skills/volume-control
```

### On a device (clone + register)

The companion repo [`cloudomate/iva-hermes`](https://github.com/cloudomate/iva-hermes)
ships an `install.sh` that clones this repo onto a device and registers
`skills/` in the device's `~/.hermes/config.yaml` under
`skills.external_dirs`.

### Claude Code plugin marketplace

```
/plugin marketplace add cloudomate/skills
/plugin install iva-device-skills@cloudomate-agent-skills
```

## Creating a new skill

Copy [`template/SKILL.md`](./template/SKILL.md) into `skills/<your-skill>/SKILL.md`
and fill it in. Keep the `description` trigger-oriented (the phrases a user would
actually say), keep skills flat, and add the skill to `skills.sh.json` and (if it
should ship as a plugin) `.claude-plugin/marketplace.json`.

**Public repo — no secrets.** Backend endpoints, API keys, and device-specific
paths belong in the device's own config/SOUL, not in a skill committed here.

## License

MIT (see each skill's `license` field).
