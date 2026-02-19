# Lotstech Skills

Official agent skills for the [Lotstech](https://lotstech.com) ecosystem. Add these skills to any MCP-compatible AI agent to let it work as a teammate across Lotstech apps.

## What are Skills?

Skills are instruction packages that give AI agents domain knowledge and structured guidance for using a specific platform. When an agent has a skill loaded, it knows what tools are available, how to use them, and how to follow the platform's conventions — without needing manual prompting every time.

## Available Skills

| Skill | App | MCP Endpoint | Description |
|-------|-----|--------------|-------------|
| [lots-team](./lots-team/) | [lots.team](https://lots.team) | `https://api.lots.team/mcp` | Project management, tasks, feedback, CRM, changelog, knowledge base |
| [lots-social](./lots-social/) | [lots.social](https://lots.social) | `https://api.lots.social/mcp` | Social media management, content scheduling, team approval workflows |
| [lots-blog](./lots-blog/) | [lots.blog](https://lots.blog) | `https://api.lots.blog/mcp` | Blog platform, multi-type posts, topics, team collaboration |
| [lots-link](./lots-link/) | [lots.link](https://lots.link) | `https://api.lots.link/mcp` | Link shortening, analytics, UTM tracking, custom domains |

## Compatible Agent Platforms

These skills work with any MCP-compatible agent platform:

- **[Lots Agent](https://lotsagent.com)** — Lotstech's own agent platform, built-in skill and MCP support
- **[OpenClaw](https://openclaw.ai)** — Add skills directly from your agent dashboard
- **[Claude Code](https://claude.ai/code)** — Install via `npx skills add lotstech/lots-team`
- **Cursor, Windsurf, Cline** — Any editor with MCP support
- Any agent that supports the Model Context Protocol

## Quick Install

```bash
# Install individual skills
npx skills add lotstech/lots-team
npx skills add lotstech/lots-social
npx skills add lotstech/lots-blog
npx skills add lotstech/lots-link
```

## How It Works

1. **Get your API key** — Sign in to any Lotstech app → Settings → API Keys
2. **Connect the MCP server** — Add the app's MCP endpoint to your agent config
3. **Add the skill** — Load the skill so your agent knows how to work on the platform
4. **Start collaborating** — Your agent works as a real team member alongside you

### Example MCP Config (lots.team)

```json
{
  "mcpServers": {
    "lots-team": {
      "type": "http",
      "url": "https://api.lots.team/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

## About Lotstech

Lotstech builds AI-native collaboration tools where humans and AI agents work together as genuine teammates. All apps share a unified identity system — one account works across the entire ecosystem.

- **Website**: [lotstech.com](https://lotstech.com)
- **Lots Agent**: [lotsagent.com](https://lotsagent.com) — Create and manage your own AI agents
- **GitHub**: [github.com/lotstech](https://github.com/lotstech)
