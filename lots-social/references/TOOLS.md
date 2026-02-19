# lots.social — MCP Tools Reference

All tools available via `https://api.lots.social/mcp`. Tools are called by their **Tool Slug**.

---

## Workspaces

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_workspaces` | List all workspaces where user is a member | — |
| `get_workspace_details` | Get comprehensive workspace details, settings, and user role | `workspace_id` |
| `create_workspace` | Create a new team workspace | `name` (required), `slug?`, `description?`, `avatar_url?` |
| `update_workspace` | Update workspace name, description, or settings | `workspace_id`, `name?`, `description?` |
| `delete_workspace` | Permanently delete a workspace (Owner only) | `workspace_id` |

---

## Posts

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_posts` | List posts filtered by type (draft/scheduled/posted) | `type` (required), `workspace_id?`, `created_by?`, `limit?`, `offset?` |
| `get_post` | Get complete post details including media, platforms, and logs | `post_id` |
| `create_post` | Create a draft, scheduled, or immediately published post | See below |
| `update_post` | Update draft or scheduled post (cannot edit published) | `post_id` (required), `caption?`, `platforms?`, `media_ids?`, `scheduled_time?`, `title?`, `link?` |
| `delete_post` | Permanently delete a post | `post_id` |
| `cancel_scheduled_post` | Cancel a scheduled post and revert to draft | `post_id` |

### `create_post` Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `caption` | ✅ | Main text content (max 5000 chars; platform limits apply) |
| `type` | — | `"draft"` (default), `"scheduled"`, or `"posted"` |
| `platforms` | Required for scheduled/posted | Array of connected account UUIDs |
| `scheduled_time` | Required if type=scheduled | ISO 8601 datetime, at least 5 min in future |
| `media_ids` | — | Array of pre-uploaded media UUIDs (max 10) |
| `workspace_id` | — | UUID if this is a team workspace post |
| `title` | — | Required for YouTube/LinkedIn posts (max 100 chars) |
| `link` | — | Optional URL to include |

**Auto-type logic:** If `scheduled_time` provided → `scheduled`. If `platforms` provided (no schedule) → `posted`. Otherwise → `draft`.

---

## Connected Accounts

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_connected_accounts` | List all connected social media accounts | `workspace_id?` |
| `get_account_details` | Get details, health metrics, and usage stats for an account | `account_id` |
| `disconnect_account` | Permanently remove a connected account | `account_id` |
| `refresh_account_token` | Manually refresh an expiring OAuth token | `account_id` |

---

## Media Library

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_media` | List uploaded media files (images and videos) | `workspace_id?`, `type?` (image/video), `limit?` |
| `get_media_details` | Get file details, usage stats, and posts using this media | `media_id` |
| `delete_media` | Delete a media file (only if not used in scheduled/published posts) | `media_id` |

> **Note:** Media cannot be uploaded via MCP. Use the lots.social web app to upload files, then reference their UUIDs in `create_post`.

---

## Comments

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_comments` | List all comments for a post (ordered chronologically) | `post_id` |
| `add_comment` | Add a comment or reply to a post | `post_id`, `content`, `workspace_id?`, `parent_comment_id?` (for replies) |
| `update_comment` | Edit comment content (author only) | `comment_id`, `content` |
| `delete_comment` | Delete a comment (author or Manager+) | `comment_id` |

---

## Approval Workflow

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_approval_status` | Get current approval status of a post | `post_id`, `workspace_id` |
| `update_approval_status` | Change approval status | `post_id` (required), `workspace_id` (required), `action` (required), `comment?` |

### Approval Actions

| Action | Who Can Use | Result |
|--------|-------------|--------|
| `request_approval` | Post creator only | Status → `pending` |
| `approve` | Manager, Admin, Owner | Status → `approved` |
| `reject` | Manager, Admin, Owner | Status → `rejected` |

Approval flow: `none` → `pending` → `approved` or `rejected`

---

## Analytics

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_post_analytics` | Engagement metrics for a published post (likes, comments, shares, impressions) | `post_id` |
| `get_aggregate_analytics` | Workspace-level analytics with top posts and platform breakdown | `workspace_id?`, `date_from?`, `date_to?`, `platform?` |

---

## Team

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_workspace_members` | List all workspace members with roles and status | `workspace_id`, `status?` (active/pending/inactive) |
