# lots.social — MCP Tools Reference

All tools available via `https://api.lots.social/mcp`. Tools are called by their **Tool Slug**.

---

## Teammate Workflow

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_social_operating_brief` | Canonical teammate-v3 instructions, readiness, and exact brand Campaign/Plan context; call first after choosing one brand | `brand_id`, `workspace_id?` |
| `run_social_teammate_workflow` | Preferred high-level v3 workflow: requires one active campaign, creates a rolling sprint of at most seven days, freezes exact direction/plan/playbook context, writes, independently reviews, rewrites at most twice, prepares approval, and schedules approved work | `brand_id`, `workspace_id?`, `idempotency_key?`, `max_transitions?`, `max_new_items?`, `shadow?` |
| `evaluate_social_autopilot` | Read-only health/next-action inspection for agents by default | `brand_id`, `workspace_id?`, `source="agent"` |
| `create_social_content_request` | Structured, deduplicated owner question with open-request budget | `brand_id`, `question`, `answer_type`, `options?`, `example_answer?`, `fact_keys?`, `blocking_item_ids?` |

## Business Profile, Social Direction, and Campaigns

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_business_profile` / `save_business_profile` | Read or update durable factual brand context | `brand_id`, `workspace_id?`, `profile?` |
| `get_social_direction` / `save_social_direction` | Read or version the simple long-term north star; never store platform tactics here | `brand_id`, `objective?`, `primary_audience?`, `desired_action?`, `constraints?` |
| `list_social_campaigns` / `get_social_campaign` | Find campaign history or load the exact active/draft campaign with plan and sprints | `brand_id`, `campaign_id?`, `status?` |
| `create_social_campaign` | Create a focused or Always-on draft linked to current Social Direction | `brand_id`, `name`, `goal_outcome`, `campaign_kind`, account scope/timeframe fields |
| `assess_social_campaign_readiness` | Bounded intelligent discovery before planning; at most two requirements | `campaign_id`, `business_note?` |
| `assess_social_campaign_plan_health` | Dated reassessment for an active Always-on campaign; preserves original discovery and recommends a new Plan version only when warranted | `campaign_id`, `business_note?` |
| `build_social_campaign_plan` / `save_social_campaign_plan` | Build or save the canonical account-level plan after readiness | `campaign_id`, `business_note?`, `plan?` |
| `activate_social_campaign` | Activate a ready campaign; ends the brand’s previous active campaign | `campaign_id` |
| `get_social_campaign_results` | Recalculate exact live campaign results; remains dynamic after campaign end | `campaign_id`, `refresh?` |
| `generate_social_campaign_retrospective` | Save a dated evidence-bound AI assessment while analytics remain live | `campaign_id`, `final?` |
| `save_campaign_owner_reflection` | Save the owner’s own conclusion separately | `campaign_id`, `owner_reflection` |
| `end_social_campaign` | End only after the owner explicitly confirms closure | `campaign_id` |
| `get_platform_playbook` | Read versioned official/observed/experimental guidance | `platform`, `version?` |

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
| `create_post` | Create a draft or scheduled post with enforced brand/account scope | See below |
| `update_post` | Update draft or scheduled post (cannot edit published) | `post_id` (required), `caption?`, `platforms?`, `media_ids?`, `scheduled_time?`, `title?`, `link?` |
| `delete_post` | Permanently delete a post | `post_id` |
| `cancel_scheduled_post` | Cancel a scheduled post and revert to draft | `post_id` |

### `create_post` Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `caption` | ✅ | Main text content (max 5000 chars; platform limits apply) |
| `brand_id` | Required for teammate use | Selected immutable brand UUID |
| `type` | — | `"draft"` (default) or `"scheduled"` |
| `platforms` | ✅ | Array of connected account UUIDs from the selected brand |
| `scheduled_time` | Required if type=scheduled | ISO 8601 datetime, at least 5 min in future |
| `media_ids` | — | Array of pre-uploaded media UUIDs (max 10) |
| `workspace_id` | — | UUID if this is a team workspace post |
| `title` | — | Required for YouTube/LinkedIn posts (max 100 chars) |
| `link` | — | Optional URL to include |
| `standalone` | — | Set `true` only for an explicitly requested one-off draft outside the active campaign; cannot be combined with `campaign_id` |

**Type logic:** If `scheduled_time` is provided the post is scheduled; otherwise it is
a draft. `posted` is rejected because publishing must use the validated product pipeline.

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
| `upload_media` | Store a generated/supplied image in the LotsSocial media library with searchable context | `image_url` or `image_base64`, `workspace_id?`, `description?`, `alt_text?` |
| `list_media` | List uploaded media files (images and videos) | `workspace_id?`, `type?` (image/video), `limit?` |
| `get_media_details` | Get file details, usage stats, and posts using this media | `media_id` |
| `delete_media` | Delete a media file (only if not used in scheduled/published posts) | `media_id` |

> Use `upload_media` when the generated/supplied asset is available to the agent. Otherwise ask the owner to upload it in LotsSocial, then locate it with `list_media` and reference its UUID in `create_post`.

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
| `set_social_post_approval` | Change approval status; teammate posts require a fresh passing independent review and every post account is verified against the immutable brand | `post_id` (required), `workspace_id` (required), `brand_id` (required for teammate use), `action` (required), `comment?` |

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
