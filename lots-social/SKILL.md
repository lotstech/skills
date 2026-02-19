---
name: lots-social
description: Work as a teammate on lots.social — create social media posts, schedule content, manage multi-platform publishing, review team posts, and analyze performance via MCP. Use this skill when collaborating on social media management, content strategy, or team content workflows.
compatibility: Works with any MCP-compatible agent (Lots Agent, OpenClaw, Claude Code, Cursor, Windsurf, and more)
metadata:
  author: lotstech
  version: "1.0"
  platform: lots.social
  mcp_endpoint: https://api.lots.social/mcp
---

# lots.social Teammate Skill

You are working as an AI teammate on **lots.social** — a social media management platform where humans and AI agents collaborate on content creation, scheduling, and publishing across multiple social platforms.

## Connecting to lots.social

**MCP Endpoint:** `https://api.lots.social/mcp`

**Authentication:** Bearer token (API key from Settings → API Keys)

```json
{
  "mcpServers": {
    "lots-social": {
      "type": "http",
      "url": "https://api.lots.social/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

## Platform Structure

```
User Account
└── Workspaces (personal or team)
    ├── Members (with roles: Owner, Admin, Manager, Creator)
    ├── Connected Accounts (Twitter/X, Instagram, LinkedIn, Facebook, etc.)
    ├── Posts (draft / scheduled / posted)
    │   ├── Comments (team discussion)
    │   ├── Approval Status (none → pending → approved/rejected)
    │   └── Analytics (engagement metrics)
    └── Media Library (uploaded images/videos)
```

## Your Role

When you connect, you join a **Workspace** with a specific role:

- **Owner / Admin** — Full workspace management, can approve/reject posts
- **Manager** — Can approve/reject posts, manage team
- **Creator** — Can create posts, request approval; cannot approve own posts

Always call `list_workspaces` first to get your workspace context and understand which platforms are connected.

## What You Can Do

### Content Creation
- **Draft posts** — Create post drafts with caption, media, links, and platform targets
- **Schedule posts** — Set a future `scheduled_time` (ISO 8601, at least 5 minutes ahead)
- **Publish immediately** — Create posts with `type: "posted"` to publish right away
- **Multi-platform** — Post to multiple connected accounts simultaneously via `platforms` array

### Content Management
- **Update drafts and scheduled posts** — Edit caption, media, platforms, or schedule time
- **Cancel scheduling** — Use `cancel_scheduled_post` to revert a scheduled post to draft
- **Delete posts** — Remove drafts, scheduled, or published posts (with appropriate permissions)

### Team Collaboration
- **Comments** — Add internal comments on posts for team discussion
- **Approval workflow** — Request approval (`request_approval`), then managers approve or reject with optional comment

### Analytics
- **Post analytics** — Get engagement metrics (likes, comments, shares, impressions) for published posts
- **Aggregate analytics** — Workspace-level performance overview with top posts and platform breakdown
- **Date filtering** — Analyze specific time periods for trend insights

### Account Management
- **Connected accounts** — List and monitor connected social media accounts
- **Token refresh** — Refresh expiring OAuth tokens to prevent disconnections

## Working Principles

1. **Draft first, publish second** — Unless explicitly asked to publish immediately, save as draft so humans can review
2. **Check connected accounts** — Call `list_connected_accounts` to know which platforms are available before creating posts
3. **Respect the approval workflow** — If the workspace uses approval flows, call `update_approval_status` with `request_approval` after creating a post, don't self-approve
4. **Platform character limits** — Twitter/X: 280 chars, Instagram: 2,200 chars, LinkedIn: 3,000 chars. Validate caption length per platform
5. **Media must be pre-uploaded** — Use `list_media` to find available media UUIDs; you cannot upload directly via MCP

## Reference Files

- **[references/TOOLS.md](references/TOOLS.md)** — Complete tool reference organized by category with key parameters
- **[references/WORKFLOWS.md](references/WORKFLOWS.md)** — Common workflow examples (draft → approve → publish, scheduling campaigns, analytics reporting)
