# lots.social — MCP Tools Reference

All tools available via `https://api.lots.social/mcp`. Tools are called by their **Tool Slug**.

---

## Business Profile, Brand Social Goal, and Campaigns

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_business_profile` / `save_business_profile` | Read or update the brand's two free-form markdown documents: `profile.business_profile` (public truth) and `profile.internal_guidance` (private steering, never quotable as fact). No structured sub-fields exist — do not invent them | `brand_id`, `workspace_id?`, `profile?` |
| `get_brand_social_goal` / `save_brand_social_goal` | Read or version the brand's simple long-term goal and audience; never store platform tactics here | `brand_id`, `objective?`, `primary_audience?`, `desired_action?`, `constraints?` |
| `list_social_campaigns` / `get_social_campaign` | Find campaign history or load the exact active/draft campaign with its plan | `brand_id`, `campaign_id?`, `status?` |
| `create_social_campaign` | Create a focused or Always-on campaign draft | `brand_id`, `name`, `goal_outcome`, `campaign_kind`, account scope/timeframe fields |
| `save_social_campaign_plan` | Save the per-account plan: role, audience, cadence, formats, notes | `campaign_id`, `plan` |
| `activate_social_campaign` | Activate a campaign; ends the brand's previous active campaign | `campaign_id` |
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

---

## Posts

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_social_posts` | List posts filtered by type (draft/scheduled/posted) | `type` (required), `workspace_id?`, `created_by?`, `limit?`, `offset?` |
| `get_social_post` | Get complete post details including media, platforms, and logs | `post_id` |
| `create_social_post` | Create a draft or scheduled post with enforced brand/account scope | See below |
| `update_social_post` | Update draft or scheduled post (cannot edit published) | `post_id` (required), `caption?`, `platforms?`, `media_ids?`, `scheduled_time?`, `title?`, `link?` |
| `delete_social_post` | Permanently delete a post | `post_id` |
| `cancel_scheduled_post` | Cancel a scheduled post and revert to draft | `post_id` |

### `create_social_post` Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `caption` | ✅ | Main text content (max 5000 chars; platform limits apply) |
| `brand_id` | Required | Selected immutable brand UUID |
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

---

## Media Library

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `upload_media` | Store a generated/supplied image in the LotsSocial media library with searchable context | `image_url` or `image_base64`, `workspace_id?`, `description?`, `alt_text?` |
| `list_media` | Search/list uploaded images and videos | `workspace_id?`, `file_type?` (image/video), `search?`, `limit?` |
| `get_media_details` | Get file details, usage stats, and posts using this media | `media_id` |
| `update_media_metadata` | Improve searchable description, alt text, or tags | `media_id`, `workspace_id?`, `description?`, `alt_text?`, `tags?` |
| `delete_media` | Delete a media file (only if not used in scheduled/published posts) | `media_id` |

> Use `upload_media` when the generated/supplied asset is available to the agent. Otherwise ask the owner to upload it in LotsSocial, then locate it with `list_media` and reference its UUID in `create_social_post`.

---

## Comments

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_comments` | List all comments for a post (ordered chronologically) | `post_id` |
| `add_comment` | Add a comment or reply to a post | `post_id`, `content`, `workspace_id?`, `parent_comment_id?` (for replies) |
| `delete_comment` | Delete a comment (author or Manager+) | `comment_id` |

---

## Approval Workflow

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_approval_status` | Get current approval status of a post | `post_id`, `workspace_id` |
| `set_social_post_approval` | Change approval status; AI-written posts require a fresh passing independent review and every post account is verified against the immutable brand | `post_id` (required), `workspace_id` (required), `brand_id` (required), `action` (required), `comment?` |

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
