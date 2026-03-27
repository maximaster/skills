# Skills

A collection of reusable AI-agent [skills](https://agentskills.io/specification), also available as a [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces).

## Installation

### Claude Code Plugin Marketplace

Add the marketplace, then install plugins:

```bash
claude plugin marketplace add https://github.com/maximaster/skills
claude plugin install chronic
```

### agentskills.io

```bash
npx skills add maximaster/skills@<skill-name>
```

## Available Skills

- [chronic](chronic/): Guides the agent to use [chronic](https://github.com/docwhat/chronic) for suppressing noisy command output. Silent on success, verbose on failure.
