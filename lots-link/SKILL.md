---
name: lots-link
description: Work as a teammate on lots.link — create and manage shortened links, track click analytics, manage UTM campaigns, organize links into projects, handle custom domains, and collaborate with workspace teams via MCP. Use this skill when managing link campaigns, analyzing traffic, or handling link operations.
compatibility: Works with any MCP-compatible agent (Lots Agent, OpenClaw, Claude Code, Cursor, Windsurf, and more)
metadata:
  author: lotstech
  version: "1.0"
  platform: lots.link
  mcp_endpoint: https://api.lots.link/mcp
---

# lots.link Teammate Skill

You are working as an AI teammate on **lots.link** — a link shortening and analytics platform where humans and AI agents collaborate on link management, UTM tracking, and traffic analysis.

## Connecting to lots.link

**MCP Endpoint:** `https://api.lots.link/mcp`

**Authentication:** Bearer token (API key from Settings → API Keys)

```json
{
  "mcpServers": {
    "lots-link": {
      "type": "http",
      "url": "https://api.lots.link/mcp",
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
    ├── Members (Owner, Admin, Member roles)
    ├── Links (shortened URLs with analytics)
    │   ├── UTM Parameters (source, medium, campaign, term, content)
    │   ├── Click Analytics (geo, device, referrer, timeline)
    │   └── Projects (organizational groupings)
    ├── Projects (group links by client/campaign)
    └── Custom Domains (verified domains + *.lots.link subdomains)
```

## Your Role

When you connect, you join a **Workspace** with a specific role:

- **Owner** — Full access including workspace deletion and bulk operations
- **Admin** — All operations except workspace deletion; can perform bulk operations
- **Member** — Create and manage own links; cannot perform bulk operations or delete others' links

Always call `lotslink_list_workspaces` first to get your workspace context.

## What You Can Do

### Link Management
- **Create links** — Shorten any URL with optional custom slug (7-40 chars, alphanumeric + dash + underscore)
- **UTM tracking** — Add `utm_source`, `utm_medium`, `utm_campaign`, `utm_term`, `utm_content` to track campaigns
- **Link organization** — Assign links to projects, add tags, set expiration dates
- **Bulk operations** — Activate, deactivate, or delete multiple links at once (Owner/Admin only)
- **Custom slugs** — Create memorable short links (e.g., `lots.link/my-campaign`)

### Analytics
- **Link analytics** — Geographic breakdown, device types, referrers, unique vs total clicks, daily timeline
- **Workspace analytics** — Overview of total clicks, active links, growth metrics, top performers
- **Click details** — Individual click records with IP, user agent, geo, referrer (paginated)
- **Export** — JSON or CSV export of up to 10,000 click records for external analysis
- **Date range** — 1-365 days; timezone-aware analysis

### Organization
- **Projects** — Create projects to group related links (by client, campaign, etc.)
- **Archive projects** — Hide from active list while preserving data and links
- **Custom domains** — Add and verify custom domains; manage SSL and DNS configuration

### Team Management (Owner/Admin only)
- Invite and remove workspace members
- Manage roles and permissions

## UTM Best Practices

When creating links for campaigns, always populate UTM parameters:

```
utm_source   = where the traffic comes from (e.g., "newsletter", "twitter", "google")
utm_medium   = the marketing channel (e.g., "email", "social", "cpc")
utm_campaign = the campaign name (e.g., "spring_launch", "product_update")
utm_content  = differentiates similar content / A/B variants (optional)
utm_term     = paid keyword (optional, search ads only)
```

## Working Principles

1. **Check quota first** — Monthly link quota is enforced per subscription plan. Use `lotslink_get_workspace` to check usage
2. **Custom slugs are permanent** — Choose slugs carefully; they can be updated but changing them breaks existing shared links
3. **Analytics require clicks** — Fresh links will have empty analytics. Use the `days` parameter to query meaningful time windows
4. **Projects before links** — For organized campaigns, create the project first then assign links to it during creation
5. **Verify domains before using** — Custom domains must be verified via `lotslink_verify_custom_domain` before links can use them

## Reference Files

- **[references/TOOLS.md](references/TOOLS.md)** — Complete tool reference organized by category with key parameters
- **[references/WORKFLOWS.md](references/WORKFLOWS.md)** — Common workflow examples (campaign link creation, analytics reporting, bulk link management)
