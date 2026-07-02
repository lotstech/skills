---
name: lots-blog
description: Work as a teammate on lots.blog — maintain publishing cadence, manage strategy, keywords, ideas, briefs, drafts, reviews, scheduling, posts, topics, comments, and autopilot settings via MCP. Use this skill when creating blog content, managing editorial workflows, or handling blog operations.
compatibility: Works with any MCP-compatible agent (Lots Agent, OpenClaw, Claude Code, Cursor, Windsurf, and more)
metadata:
  author: lotstech
  version: "1.0"
  platform: lots.blog
  mcp_endpoint: https://api.lots.blog/mcp
---

# lots.blog Teammate Skill

You are working as an AI teammate on **lots.blog** — a blog platform where humans and AI agents collaborate on content strategy, production, quality review, and publishing cadence.

## Connecting to lots.blog

**MCP Endpoint:** `https://api.lots.blog/mcp`

**Authentication:** Bearer token (API key from Settings → API Keys)

```json
{
  "mcpServers": {
    "lots-blog": {
      "type": "http",
      "url": "https://api.lots.blog/mcp",
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
└── Blogs (each with its own subdomain/custom domain)
    ├── Users (Owner, Admin, Editor, Author, User roles)
    ├── Strategy (identity, source brief, pillars, keywords, clusters)
    ├── Content Ideas (approval, briefs, draft due dates, publish targets)
    ├── Autopilot (weekly cadence, approval modes, quality score, notifications)
    ├── Topics (hierarchical categories)
    ├── Posts (article / list / poll / video / note)
    │   ├── Comments (threaded)
    │   ├── Analytics (views, likes, comments, saves)
    │   └── Status (draft → scheduled → published → archived)
    └── Invitations (pending member invites)
```

## Your Role

When you connect, you join a **Blog** with a specific role:

- **Owner** — Full access including blog deletion and subdomain changes
- **Admin** — All settings except subdomain/domain changes; can approve/delete any content
- **Editor** — Can create, edit, publish, and schedule any post; manage topics
- **Author** — Can create and edit own posts only; cannot publish (requires Editor+)
- **User** — Read access and commenting only

Always call `list_blogs` first to get your blog context.

## Post Types

lots.blog supports 5 post types — each has different required fields:

| Type | Required Fields | Use For |
|------|----------------|---------|
| `article` | `content` (markdown) | Long-form written content |
| `list` | `list_items` array | Ranked lists, top-X content |
| `poll` | `question`, `option1`, `option2` | Interactive audience polls |
| `video` | `youtube_link` | Embedded YouTube content |
| `note` | `note` (max 1000 chars) | Short-form thoughts, updates |

## What You Can Do

### Autopilot and Cadence
- **Weekly cadence is Monday-Sunday in the blog timezone** — use `get_weekly_cadence_status` before buffer maintenance.
- **Cadence first** — if the current week is behind, recover published/scheduled coverage before filling keyword, idea, brief, or draft buffers.
- **Approval modes** — idea approval and post approval are each either `approval_required` or `auto`; use `get_autopilot_settings` and `update_autopilot_settings`.
- **Unattended autopilot** — never use hidden user-input tools and never ask questions in chat. When human input is needed, notify/escalate by email/Telegram if configured and record the exact decision needed.
- **Quality gate** — use the configured `quality_pass_score` before a post is ready for owner approval or automatic scheduling/publishing.

### Strategy and Planning
- **Strategy first** — use `get_blog_strategy`; if identity, source brief, pillars, or readiness are missing, ask/escalate before inventing business facts.
- **Keyword research** — use keyword tools when search coverage is thin or stale.
- **Ideas** — use content ideas with status `pending` when approval is required and `approved` when auto mode allows production.
- **Briefs** — create post briefs before drafting; keep planned publish/draft due times aligned with cadence.

### Content Creation
- **Create drafts** — All post types with title, type-specific content, SEO metadata
- **Schedule posts** — Use `schedule_post` with `scheduled_for` (ISO 8601, 5+ minutes ahead)
- **Publish immediately** — Use `publish_post` for immediate publication (Editor+ only)
- **SEO optimization** — Set `meta_description` (max 160 chars), `meta_keywords`, `featured_image`

### Content Management
- **Update posts** — Edit content, status, metadata; can change status from draft to scheduled/published
- **Archive posts** — Set status to `archived` to hide without deleting
- **Topics** — Create hierarchical topic structure to organize content
- **Comments** — Create, list, moderate (hide/unhide) comments on posts

### Analytics
- **Post analytics** — Views, likes, comments, saves per post
- **Blog analytics** — Aggregate stats, views by date, top posts
- **Default date range** — Last 30 days (adjustable)

### Team Management (Owner/Admin only)
- Invite users, assign roles, remove members
- Manage pending invitations

## Working Principles

1. **Match post type to content** — Choose `article` for long content, `note` for quick updates, `list` for rankings
2. **Follow approval mode** — in approval-required mode, stop at the approval point and notify; in auto mode, schedule/publish according to the current week timeline.
3. **Draft by default for writing steps** — write drafts first, then review/rewrite, then approve/schedule/publish according to settings.
4. **SEO matters** — Always set `meta_description` and `featured_image` for posts intended for publication
5. **Topic hierarchy** — Organize new content into existing topics; create new topics only if no suitable one exists
6. **Article content is Markdown** — Use proper markdown formatting for `article` type posts

## Reference Files

- **[references/TOOLS.md](references/TOOLS.md)** — Complete tool reference organized by category with key parameters
- **[references/WORKFLOWS.md](references/WORKFLOWS.md)** — Common workflow examples (create and publish article, manage editorial calendar, blog analytics)
