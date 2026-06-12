# Security Policy

## Supported Versions

| Version | Supported |
|---------|-----------|
| 1.x     | ✅ Active  |

## Reporting a Vulnerability

**Do not open a public GitHub Issue for security vulnerabilities.**

Use [GitHub's private vulnerability reporting](https://github.com/SamurAIGPT/Generative-Media-Skills/security/advisories/new) to report issues confidentially.

Include in your report:
- Which skill file is affected
- A description of the vulnerability and potential impact
- Steps to reproduce (if applicable)
- Any suggested fix

You can expect an acknowledgement within 48 hours and a resolution within 7 days.

## Scope

Generative Media Skills is a collection of instruction files (SKILL.md) and shell scripts for AI agents.
It delegates all media generation to [muapi.ai](https://muapi.ai) via the muapi-cli.
It does not handle user authentication, store credentials, or connect to any services directly.
Your `MUAPI_API_KEY` is passed via environment variable and never logged or stored by this repository.
