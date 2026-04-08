# curious-edge: Skills Gallery for Google AI Edge

Converted [curious-stack](https://github.com/anthropics/curious-stack) quality-analysis skills into **Google AI Edge Gallery** compatible `SKILL.md` format, ready for on-device use with **Gemma 4** (E2B/E4B) via **LiteRT-LM**.

## Skills Included

| Skill | File | Purpose |
|-------|------|---------|
| AI Slop Detector | [`skills/ai-slop-detector.SKILL.md`](skills/ai-slop-detector.SKILL.md) | Detect AI-generated filler, cliches, and low-effort writing |
| Full Review | [`skills/full-review.SKILL.md`](skills/full-review.SKILL.md) | Comprehensive writing quality review |
| Claim Checker | [`skills/claim-checker.SKILL.md`](skills/claim-checker.SKILL.md) | Verify factual claims and flag unsupported assertions |
| Jargon Detector | [`skills/jargon-detector.SKILL.md`](skills/jargon-detector.SKILL.md) | Identify unnecessary jargon and suggest plain-language alternatives |
| Tone Analyzer | [`skills/tone-analyzer.SKILL.md`](skills/tone-analyzer.SKILL.md) | Analyze writing tone and consistency |
| Readability Scorer | [`skills/readability-scorer.SKILL.md`](skills/readability-scorer.SKILL.md) | Score readability and suggest simplifications |

## How to Use

### Import into Google AI Edge Gallery

1. **From URL** -- In the Gallery app, go to Agent Skills > tap **+** > choose **Load from URL** and paste the raw GitHub link to any `SKILL.md` file above.
2. **Local import** -- Download a `SKILL.md` file and import it via **Import local file** in the Gallery app.
3. **Natural invocation** -- Chat naturally (e.g. "Check this text for AI slop") or reference the skill by name.

### Requirements

- Google AI Edge Gallery app ([Android](https://play.google.com/store/apps/details?id=com.google.aiedge.gallery) / iOS)
- Gemma 4 E2B or E4B model downloaded in-app

### Advanced: LiteRT-LM CLI / SDK

For programmatic use, load these skills as system-prompt extensions with LiteRT-LM's tool-calling support. See [`docs/litert-integration.md`](docs/litert-integration.md) for details.

## Skill Format

Each `SKILL.md` follows the Gallery Agent Skill specification:

```
---
name: skill-name
description: Short description
version: 1.0.0
author: curious-stack
---

# When to Use
<trigger conditions>

# Instructions
<full analysis instructions>

# Output Contract
<structured output format>
```

## Adapting More Skills

To convert additional curious-stack skills:

1. Extract the skill's system prompt / instructions
2. Wrap in the SKILL.md metadata format above
3. Define clear trigger conditions in "When to Use"
4. Keep the output contract strict (verdict + evidence + fix)
5. Test in Gallery with sample text

## License

MIT
