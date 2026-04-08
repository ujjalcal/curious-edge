# LiteRT-LM Integration Guide

How to use curious-edge skills programmatically with LiteRT-LM outside the Gallery app.

## Overview

The Gallery app provides the easiest path for using these skills interactively. For automation, batch processing, or custom app integration, use LiteRT-LM directly with these skills as system-prompt extensions.

## Approach 1: System Prompt Injection

Load a SKILL.md file and inject its content into the system prompt:

```python
import litert_lm

# Load the skill
with open("skills/ai-slop-detector/SKILL.md") as f:
    skill_content = f.read()

# Strip YAML frontmatter, keep instructions + output contract
import re
skill_body = re.sub(r'^---\n.*?\n---\n', '', skill_content, flags=re.DOTALL)

# Create session with skill as system context
session = litert_lm.Session(
    model="gemma-4-e4b",
    system_prompt=f"You have the following skill available:\n\n{skill_body}"
)

# Invoke naturally
response = session.chat("Check this text for AI slop:\n\n" + user_text)
```

## Approach 2: Tool Calling (Function Calling)

Convert skills into tool definitions for structured invocation:

```python
import litert_lm
import json

# Define the skill as a tool
tools = [
    {
        "name": "ai_slop_detector",
        "description": "Detect AI-generated filler, cliches, and low-effort writing",
        "parameters": {
            "type": "object",
            "properties": {
                "text": {
                    "type": "string",
                    "description": "The text to analyze for AI slop patterns"
                }
            },
            "required": ["text"]
        }
    },
    {
        "name": "claim_checker",
        "description": "Verify factual claims and flag unsupported assertions",
        "parameters": {
            "type": "object",
            "properties": {
                "text": {
                    "type": "string",
                    "description": "The text containing claims to verify"
                }
            },
            "required": ["text"]
        }
    }
]

session = litert_lm.Session(
    model="gemma-4-e4b",
    tools=tools
)

# The model can now decide to invoke skills based on user input
response = session.chat("Review this article for quality issues: ...")
```

## Approach 3: Kotlin/Android Integration

For Android apps using LiteRT-LM:

```kotlin
val skillContent = assets.open("skills/full-review/SKILL.md")
    .bufferedReader().readText()

val session = LiteRtLmSession.Builder()
    .setModel("gemma-4-e4b")
    .setSystemPrompt("You have the following skill:\n\n$skillContent")
    .build()

val response = session.chat("Full review of this text: $userText")
```

## Batch Processing

Run a skill across multiple files:

```python
import litert_lm
from pathlib import Path

# Load skill
with open("skills/readability-scorer/SKILL.md") as f:
    skill = f.read()

session = litert_lm.Session(
    model="gemma-4-e4b",
    system_prompt=skill
)

# Process all text files in a directory
for file in Path("content/").glob("*.txt"):
    text = file.read_text()
    result = session.chat(f"Score the readability of this text:\n\n{text}")
    print(f"\n{'='*60}\n{file.name}\n{'='*60}")
    print(result)
```

## Performance Notes

- **E2B** (2B parameters): Fast inference, good for simpler skills (jargon-detector, readability-scorer). May struggle with nuanced analysis (claim-checker, tone-analyzer).
- **E4B** (4B parameters): Better reasoning, recommended for all skills. Slightly slower but significantly more capable for complex analysis.
- **Context length**: On-device models have constrained context. For long documents, consider chunking text and analyzing sections independently.
- **First-token latency**: Initial response may take 1-3 seconds on mobile. Subsequent tokens stream quickly.

## Tips

1. **One skill per session**: For best results, load only the skill you need rather than all skills at once. This keeps the system prompt focused and improves output quality.
2. **Structured output**: The output contracts in each SKILL.md help the model produce consistent, parseable results. You can post-process the markdown output programmatically.
3. **Temperature**: Use temperature 0.2-0.4 for analysis skills. Higher temperatures reduce consistency of structured output.
4. **Chunking**: For texts longer than ~2000 words, split into sections and analyze each separately, then combine results.
