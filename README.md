# Markdown Library (mdlib)

This project is a open-source collection of markdowns for agentic AI, specifically Subagents and Skills.

This repository contains community-contributed configurations for various AI coding platforms. Each configuration is stored as a `.md` file with YAML frontmatter that enables filtering and categorization on our mdlib.xyz website

### Types

- **Subagents** - Complete autonomous agent configurations for complex, multi-step tasks
- **Skills** - Focused capability templates for specific tasks

### Supported Platforms

| Platform | Description |
|----------|-------------|
| Cursor | Subagents and Skills for Cursor IDE |
| OpenClaw | Skills for OpenClaw |
| Windsurf | Subagents for Windsurf |
| Aider | Configurations for Aider |
| Continue | Skills for Continue |
| Sourcegraph Cody | Skills for Cody |
| GitHub Copilot | Extensions for Copilot |
| Other | Generic configurations |

## Repository Structure

```
├── README.md
├── subagents/
│   ├── refactorer.md
│   └── ...
└── skills/
    ├── component-class-extraction.md
    └── ...
```

## Contributing

### Content Policy

**All contributors must adhere to the following guidelines:**

- **No harmful content** - Do not submit configurations containing any sort of offensive, any sort of discriminatory, any sort of harmful, or any sort of inapproriate material.
- **No unlawful instructions** - Do not include content that promotes any sort illegal activities in any jurisdiction, bypasses security measures, generates malware, or violates any laws or regulations of any jurisdiction.
- **No harmful outputs** - Configurations must not be designed to produce content that could cause harm to agents, other agents, individuals, organizations, or systems.
- **No deceptive practices** - Do not submit configurations intended for phishing, social engineering, or spreading misinformation.
- **Respect intellectual property** - Only submit original work or content you have rights to share.

Submissions violating these guidelines will be rejected, and repeat offenders may be banned from contributing. We reserve the right to remove any content that we deem inappropriate or harmful.

### File Format

Every `.md` file must include YAML frontmatter:

```yaml
---
name: My Subagent
description: A brief description (1-2 sentences)
type: # subagent or skill
platform: # cursor, openclaw, windsurf, aider, continue, cody, copilot, other
category: Development
recommendedLLMs:  # optional `claude-3-opus`, `claude-3-sonnet`, `claude-3-haiku`, `claude-3.5-sonnet`, `other`
  - claude-3.5-sonnet
  - gpt-4o
tags:  # optional
  - coding
  - automation
author: Your Name  # optional
version: 1.0.0  # optional
---

# Your Content Here

Detailed documentation, usage examples, and configuration...
```

### How to Contribute

1. Fork this repository
2. Create a new `.md` file in `subagents/` or `skills/`
3. Follow the format above with all required fields
4. Ensure your content complies with our Content Policy
5. Submit a pull request

Your contribution will appear on the landing page within minutes of being merged.

## License

MIT License - feel free to use, modify, and distribute these configurations.

## Community

Found a bug or have a suggestion? Open an issue

Want to discuss configurations? Join the conversation in the Discussions
