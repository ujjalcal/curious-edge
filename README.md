# curious-edge: Skills Gallery for Google AI Edge

Converted [curious-stack](https://github.com/anthropics/curious-stack) quality-analysis skills into **Google AI Edge Gallery** compatible `SKILL.md` format, ready for on-device use with **Gemma 4** (E2B/E4B) via **LiteRT-LM**.

## Skills Included

| Skill | Folder | Purpose |
|-------|--------|---------|
| AI Slop Detector | [`skills/ai-slop-detector/`](skills/ai-slop-detector/) | Detect AI-generated filler, cliches, and low-effort writing |
| Full Review | [`skills/full-review/`](skills/full-review/) | Comprehensive writing quality review |
| Claim Checker | [`skills/claim-checker/`](skills/claim-checker/) | Verify factual claims and flag unsupported assertions |
| Jargon Detector | [`skills/jargon-detector/`](skills/jargon-detector/) | Identify unnecessary jargon and suggest plain-language alternatives |
| Tone Analyzer | [`skills/tone-analyzer/`](skills/tone-analyzer/) | Analyze writing tone and consistency |
| Readability Scorer | [`skills/readability-scorer/`](skills/readability-scorer/) | Score readability and suggest simplifications |

## How to Use

### Import into Google AI Edge Gallery

Each skill lives in its own folder containing a `SKILL.md` file, matching the Gallery's expected directory structure.

**From URL** -- In the Gallery app, go to Agent Skills > tap **+** > choose **Load from URL** and paste the GitHub Pages URL for the skill folder (the app appends `/SKILL.md` automatically):

```
https://ujjalcal.github.io/curious-edge/skills/ai-slop-detector
```

All skill URLs:
- **AI Slop Detector**: `https://ujjalcal.github.io/curious-edge/skills/ai-slop-detector`
- **Full Review**: `https://ujjalcal.github.io/curious-edge/skills/full-review`
- **Claim Checker**: `https://ujjalcal.github.io/curious-edge/skills/claim-checker`
- **Jargon Detector**: `https://ujjalcal.github.io/curious-edge/skills/jargon-detector`
- **Tone Analyzer**: `https://ujjalcal.github.io/curious-edge/skills/tone-analyzer`
- **Readability Scorer**: `https://ujjalcal.github.io/curious-edge/skills/readability-scorer`

> **Note**: These use GitHub Pages (`github.io`), not `raw.githubusercontent.com`. The iOS Gallery app requires GitHub Pages URLs. A `.nojekyll` file in the repo root prevents Jekyll from converting the markdown files.

**Local import** -- Download a `SKILL.md` file and import it via **Import local file** in the Gallery app.

**Natural invocation** -- Chat naturally (e.g. "Check this text for AI slop") or reference the skill by name.

### Requirements

- Google AI Edge Gallery app ([Android](https://play.google.com/store/apps/details?id=com.google.ai.edge.gallery) / iOS)
- Gemma 4 E2B or E4B model downloaded in-app

### Advanced: LiteRT-LM CLI / SDK

For programmatic use, load these skills as system-prompt extensions with LiteRT-LM's tool-calling support. See [`docs/litert-integration.md`](docs/litert-integration.md) for details.

## Skill Format

Each `SKILL.md` follows the [Gallery Agent Skill specification](https://github.com/google-ai-edge/gallery/tree/main/skills):

```
---
name: skill-name
description: What the skill does and when to use it.
---

# Skill Title

## Instructions
<full analysis instructions>

## Output Format
<structured output format>

---
```

Key format rules:
- Frontmatter must have exactly two `---` delimiters (opening and closing)
- Only `name` and `description` are required in frontmatter
- Description should include trigger conditions (when to invoke the skill)
- Trailing `---` at end of file closes the skill definition
- Each skill must be in its own kebab-case directory with a `SKILL.md` file

## Adapting More Skills

To convert additional curious-stack skills:

1. Create a new folder in `skills/` using kebab-case
2. Add a `SKILL.md` with simple frontmatter (`name` + `description`)
3. Put trigger conditions in the `description` field
4. Include full instructions and output format
5. End the file with `---`
6. Test in Gallery with sample text

## License

MIT
