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

## Quick Start

### 1. Install Google AI Edge Gallery

- **Android**: [Google Play Store](https://play.google.com/store/apps/details?id=com.google.ai.edge.gallery)
- **iOS**: [App Store](https://apps.apple.com/us/app/google-ai-edge-gallery/id6749645337) (requires iOS 17+)

### 2. Download a model

Open the app and download **Gemma 4 E2B** (faster, 2B params) or **Gemma 4 E4B** (better reasoning, 4B params).

### 3. Import a skill

Go to **Agent Skills** > tap **+** > choose **Load from URL** and paste any URL below:

| Skill | Import URL |
|-------|------------|
| AI Slop Detector | `https://ujjalcal.github.io/curious-edge/skills/ai-slop-detector` |
| Full Review | `https://ujjalcal.github.io/curious-edge/skills/full-review` |
| Claim Checker | `https://ujjalcal.github.io/curious-edge/skills/claim-checker` |
| Jargon Detector | `https://ujjalcal.github.io/curious-edge/skills/jargon-detector` |
| Tone Analyzer | `https://ujjalcal.github.io/curious-edge/skills/tone-analyzer` |
| Readability Scorer | `https://ujjalcal.github.io/curious-edge/skills/readability-scorer` |

### 4. Use naturally

Chat naturally -- e.g., "Check this text for AI slop" or paste text and ask for a review. The model reads skill descriptions and invokes the right one automatically.

---

## Building Your Own Skills

### Skill Types

The Gallery supports three types of skills:

| Type | Files Required | Use Case |
|------|---------------|----------|
| **Text-only** | `SKILL.md` only | Persona, analysis, writing tasks -- no external calls |
| **JavaScript** | `SKILL.md` + `scripts/index.html` | API calls, computation, interactive webviews |
| **Native** | `SKILL.md` with `run_intent` | OS-level actions (email, SMS) |

Text-only skills are the simplest and most portable. That's what all the skills in this repo are.

### Directory Structure

Each skill lives in its own kebab-case folder with a `SKILL.md` file:

```
your-repo/
├── .nojekyll              # Required for GitHub Pages (prevents Jekyll processing)
├── skills/
│   ├── my-skill/
│   │   └── SKILL.md       # Required -- the skill definition
│   ├── my-js-skill/
│   │   ├── SKILL.md
│   │   └── scripts/
│   │       └── index.html  # JS entry point (for JS skills only)
│   └── another-skill/
│       └── SKILL.md
```

### SKILL.md Format

The file has two parts separated by `---` delimiters: **frontmatter** (metadata) and **instructions** (what the model follows).

```markdown
---
name: my-skill-name
description: What this skill does. Include when to trigger it.
---

# Skill Title

## Instructions
Tell the model what to do, step by step.

## Output Format
Define the exact structure you want in responses.

---
```

### Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Kebab-case identifier (e.g., `ai-slop-detector`) |
| `description` | Yes | What the skill does + when to invoke it. The model uses this to decide whether to activate the skill. |
| `metadata.require-secret` | No | Set to `true` if the skill needs an API key |
| `metadata.require-secret-description` | No | Instructions for getting the API key |
| `metadata.homepage` | No | URL -- makes the skill name clickable in the app |

**Do NOT include** `version`, `author`, or `tags` -- the parser ignores them. Keep frontmatter simple.

### How the Parser Works

The Gallery app (both Android and iOS) parses `SKILL.md` by splitting on `---`:

```
parts = content.split("---")
// parts[0] = "" (empty, before first ---)
// parts[1] = frontmatter (name, description)
// parts[2+] = instructions (rejoined with --- if your instructions contain horizontal rules)
```

The parser requires **at least two `---` lines** (producing 3+ parts). If it finds fewer, you get:

> Error parsing SKILL.md: Invalid format: Expected at least two '---' sections.

The `description` field is appended to the model's system prompt so it can decide when to invoke the skill automatically.

### Writing Good Descriptions

The `description` field is critical -- it's how the model decides whether to invoke your skill. Include:

- **What it does**: "Detect AI-generated filler, cliches, and low-effort writing patterns."
- **When to trigger**: "Use when the user asks to check text for AI slop, generic writing, boilerplate, or wants to improve originality."

Bad: `description: A writing tool.`
Good: `description: Detect AI-generated filler, cliches, and low-effort writing patterns. Use when the user asks to check text for AI slop, generic writing, boilerplate, or wants to improve originality.`

### Writing Instructions for On-Device Models

Gemma 4 E2B/E4B are capable but smaller than cloud models. Keep instructions effective:

- **Be direct**: State the task clearly up front. Avoid preamble.
- **Use structured output**: Define exact headings, labels, and formats. The model follows structure better than open-ended prose.
- **Avoid markdown code fences in output templates**: Use bold labels and plain markdown instead of nested fenced blocks. On-device models handle plain formatting more reliably.
- **Keep output concise**: Every output token costs inference time. Request only what's needed -- verdict, evidence, one fix. Not five paragraphs of analysis.
- **One skill = one job**: Don't combine multiple analysis types into a single skill. The model performs better with focused instructions.

---

## Deploying Skills

### Option 1: GitHub Pages (Recommended)

This is the only method that works reliably on both Android and iOS.

**Setup:**

1. Create a public GitHub repo
2. Add a `.nojekyll` file in the repo root (empty file -- prevents Jekyll from converting `.md` to `.html`)
3. Put each skill in `skills/<skill-name>/SKILL.md`
4. Enable GitHub Pages: **Settings > Pages > Deploy from branch > `main` / `/ (root)` > Save**
5. Wait 1-2 minutes for deployment

**Import URL format:**
```
https://<username>.github.io/<repo>/skills/<skill-name>
```

The app appends `/SKILL.md` automatically.

### Option 2: Any Web Server

Host the skill folder on any static file server (Cloudflare Pages, Netlify, your own server). The URL must serve the raw `SKILL.md` markdown file at `<base-url>/SKILL.md`.

### Option 3: Local Import (Android only)

Push the skill folder to your device via `adb`:

```bash
adb push my-skill/ /sdcard/Download/my-skill/
```

Then in the app: **Skills > + > Import local skill** and select the folder.

### What Does NOT Work

| URL Format | Works? | Why |
|-----------|--------|-----|
| `https://<user>.github.io/<repo>/skills/<skill>` | Yes | GitHub Pages serves raw markdown |
| `https://raw.githubusercontent.com/...` | Android only | iOS Gallery app fails to parse -- likely fetches folder URL first, gets 404 |
| `https://github.com/<user>/<repo>/tree/...` | No | Returns HTML page, not raw markdown |
| Branch names with `/` (e.g., `feature/my-branch`) | Unreliable | Can confuse URL parsing on some clients |

---

## Performance Tips

### Model Selection

| Model | Speed | Quality | Best For |
|-------|-------|---------|----------|
| **Gemma 4 E2B** | Fast | Good for straightforward tasks | jargon-detector, readability-scorer, ai-slop-detector |
| **Gemma 4 E4B** | Slower | Better reasoning and nuance | claim-checker, tone-analyzer, full-review |

### Input Length Limits

Tested with articles of varying length on Gemma 4 E2B (iPhone, 5G):

| Input Length | Result |
|-------------|--------|
| ~250 words | Works well. Analysis completes, output follows format. |
| ~600 words | App hung / unresponsive. Too many tokens for E2B to process + generate structured output. |

**Recommendation**: Keep input under ~300 words for E2B. E4B may handle longer inputs but will be slower.

### Speed Optimization

1. **Disable unused skills**: Each enabled skill adds to the system prompt. Disable skills you're not using in **Manage Skills** to reduce prompt size and speed up responses.
2. **Keep input text short**: On-device inference is token-bound. Shorter input = faster analysis. ~250 words is the sweet spot for E2B.
3. **Design concise output formats**: Verbose output contracts mean more tokens to generate. Request only essential fields.
4. **Chunk long documents**: For texts over ~300 words, analyze sections separately rather than the full document at once.

### Real-World Test Results

Tested the `ai-slop-detector` skill with AI-generated "listicle slop" articles (the kind of "7 Ways AI Will Change [X]" content that's everywhere):

- **E2B correctly identified**: hollow intensifiers ("mind-blowing"), AI cliche patterns ("invades every corner of daily life"), formulaic listicle structure, and generic conclusions
- **E2B missed**: cross-article pattern recognition (identical template reused for coffee/toothbrush/shower articles), the recycled "it achieves sentience" joke used as a punchline in every article, and that zero claims were verifiable
- **Verdict accuracy**: E2B rated obvious slop as MODERATE instead of PURE SLOP -- smaller models tend to be too generous. E4B is expected to be harsher and more accurate.
- **Output truncation**: Responses sometimes cut off mid-sentence due to output token limits. Shorter output formats help.

### Known Quirks

- **JS skill hallucination**: Smaller models (E2B) may try to call `run_js` with `index.html` even for text-only skills, mimicking the built-in JS skill pattern. If this happens, the call fails and the model usually falls back to text-based analysis. E4B handles this better.
- **Output format drift**: On-device models may not perfectly follow complex output templates every time. Simpler formats produce more consistent results.
- **Context constraints**: Mobile hardware limits context length. Very long skills + long input text can hang the app or degrade output quality.
- **Generous verdicts**: E2B tends to rate slop as MODERATE when it should be HEAVY or PURE SLOP. The model is polite. E4B provides more honest assessments.

---

## Advanced: LiteRT-LM SDK Integration

For programmatic use outside the Gallery app, see [`docs/litert-integration.md`](docs/litert-integration.md). Covers:

- System prompt injection (Python, Kotlin)
- Tool calling / function calling
- Batch processing across multiple files
- Temperature and chunking recommendations

---

## Adapting Existing Skills

To convert skills from other systems (Claude Code, Cursor, etc.) into Gallery format:

1. **Create the folder**: `skills/<skill-name>/SKILL.md`
2. **Add frontmatter**: Just `name` + `description` between `---` delimiters
3. **Move trigger conditions into `description`**: The Gallery uses the description for skill selection, not a separate "When to Use" section (though you can keep one in the instructions too)
4. **Simplify output format**: Remove markdown code fences from output templates. Use bold labels and plain structure instead.
5. **End with `---`**: Add a trailing `---` at the end of the file
6. **Deploy via GitHub Pages**: Enable Pages, add `.nojekyll`, push
7. **Test**: Import in the Gallery app and try with sample input

### Converting from Claude Code / Cursor Skills

Claude-style skills typically have:
- A trigger section ("When to use")
- Detailed persona/instructions
- A structured output contract

Map these to Gallery format:
- Trigger conditions -> `description` field in frontmatter
- Persona + instructions -> `## Instructions` section
- Output contract -> `## Output Format` section (simplified, no fenced blocks)

---

## Repository Structure

```
curious-edge/
├── .nojekyll                           # Prevents Jekyll processing on GitHub Pages
├── README.md                           # This file
├── docs/
│   └── litert-integration.md           # LiteRT-LM SDK usage guide
└── skills/
    ├── ai-slop-detector/SKILL.md       # Detect AI filler and cliches
    ├── full-review/SKILL.md            # Comprehensive writing review
    ├── claim-checker/SKILL.md          # Fact-check claims
    ├── jargon-detector/SKILL.md        # Flag jargon, suggest alternatives
    ├── tone-analyzer/SKILL.md          # Analyze tone and consistency
    └── readability-scorer/SKILL.md     # Grade-level scoring
```

## References

- [Google AI Edge Gallery repo](https://github.com/google-ai-edge/gallery)
- [Gallery Skills README](https://github.com/google-ai-edge/gallery/tree/main/skills)
- [Gallery Skills Discussions](https://github.com/google-ai-edge/gallery/discussions/categories/skills) (community skills)
- [Gemma 4 blog post](https://developers.googleblog.com/bring-state-of-the-art-agentic-skills-to-the-edge-with-gemma-4/)
- [Android parser source (SkillManagerViewModel.kt)](https://github.com/google-ai-edge/gallery/blob/main/Android/src/app/src/main/java/com/google/ai/edge/gallery/customtasks/agentchat/SkillManagerViewModel.kt)

## License

MIT
