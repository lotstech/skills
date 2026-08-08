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
| `update_social_campaign` | Update a draft campaign. Goal, measurement and account scope lock after activation | `campaign_id`, name/timeframe/scope fields |
| `save_social_campaign_plan` | Save the per-account plan: role, audience, cadence, formats, notes | `campaign_id`, `plan` |
| `activate_social_campaign` | Activate a campaign; ends the brand's previous active campaign | `campaign_id` |
| `get_social_campaign_results` | Recalculate exact live campaign results; remains dynamic after campaign end | `campaign_id`, `refresh?` |
| `generate_social_campaign_retrospective` | Save a dated evidence-bound AI assessment while analytics remain live | `campaign_id`, `final?` |
| `save_campaign_owner_reflection` | Save the owner’s own conclusion separately | `campaign_id`, `owner_reflection` |
| `end_social_campaign` | End only after the owner explicitly confirms closure | `campaign_id` |
| `get_platform_playbook` | Read versioned official/observed/experimental guidance | `platform`, `version?` |

---

## Workspaces and Brands

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_workspaces` | List all workspaces where user is a member | — |
| `list_brands` | List the brands in a workspace. A brand groups connected accounts; resolve one before any mutation | `workspace_id?` |

---

## Research — decide what to write before writing it

Everything above answers "what exists". These answer "what should this post look
like". Run them **before** `create_social_post`, not after.

| Tool Slug | The question it answers | Cost |
|-----------|-------------------------|------|
| `get_brand_winning_posts` | *"Which of my own posts beat my normal, and what format were they?"* | Free — first-party analytics |
| `research_public_posts` | *"What is working right now for this topic?"* | Paid provider |
| `find_peer_accounts` | *"Who should I even be watching?"* | Paid provider |
| `research_account_feed` | *"What is this named account posting, and how big are they?"* | Paid provider |
| `research_audience_voice` | *"How do real customers phrase this?"* | Paid provider, ~3× |
| `research_industry_news` | *"Did anything happen in my field I could react to?"* | Paid provider |

| Tool Slug | Key Parameters |
|-----------|----------------|
| `get_brand_winning_posts` | `brand_id?`, `platform?`, `limit?`, `date_from?`, `date_to?`, `connected_account_ids?`, `include_underperformers?` |
| `research_public_posts` | `query` (required), `platform?` / `platforms?` (enum, max 3), `time_window?` (`week`/`month`/`any`), `limit?`, `brand_id?` |
| `find_peer_accounts` | `query` (required), `platform?` / `platforms?` (enum), `compare_followers?`, `limit?` |
| `research_account_feed` | `platform` (required, enum), `handle?` or `profile_url?`, `limit?` |
| `research_audience_voice` | `query` (required), `platform?` / `platforms?` (enum), **`subreddits` required when Reddit is requested**, `sources_per_platform?`, `limit?` |
| `research_industry_news` | `query` (required), `time_window?` (`hour`/`day`/`week`/`year`/`any`), `country?` (ISO-2), `limit?` |

**Platform coverage is not the publishing list.** lots.social publishes to 12+
networks; the research provider can only search some of them, and each tool covers
a different subset. The enum in each tool's schema is authoritative — an
unsupported platform is rejected outright rather than returning an empty success.

| Tool | Platforms |
|------|-----------|
| `research_public_posts` | twitter/x, instagram, linkedin, linkedin_page, tiktok, reddit, threads, facebook, youtube |
| `research_account_feed` | twitter/x, instagram, linkedin, linkedin_page, threads |
| `find_peer_accounts` | twitter/x, instagram, tiktok, threads, linkedin_page |
| `research_audience_voice` | reddit, youtube, twitter/x |
| `research_industry_news` | none — news publishers, not social platforms |
| *No research coverage* | bluesky, mastodon, pinterest, google-business |

**There is no trending-topics tool, and that is deliberate.** The provider has no
trending feed for TikTok, YouTube, Reddit, Instagram, Facebook, Threads or
LinkedIn. X trends exist but carry no volume data and cannot be filtered by niche,
so a coffee roaster asking "what's trending" would be handed football scores. For
*"what kind of post is working right now"* use `research_public_posts` with
`time_window: "week"`, which ranks by real engagement. For *"did something happen
I could react to"* use `research_industry_news`.

`research_industry_news` notes: `time_window` has **no month option** — the
provider accepts only hour, day, week, year and any. Check `age_days` on each
article before writing "today". `dropped_offtopic` counts articles the provider
returned that never mention your query; a high number means the query is too broad
or too niche for news coverage. `recurring_terms` lists words appearing in two or
more headlines — an empty list means no repeated theme, not a failure.

**Reading the response.** Every provider-backed tool returns its own gaps:

```jsonc
{
  "platforms_returned":    [{ "platform": "reddit", "example_count": 6, "recency_applied": "sort_by=top" }],
  "platforms_empty":       [{ "platform": "facebook", "reason": "no_results" }],
  "platforms_failed":      [{ "platform": "youtube", "reason": "upstream_timeout" }],
  "platforms_unsupported": [{ "platform": "bluesky", "reason": "not_covered_by_research_provider" }],
  "platforms": ["reddit"]   // only those that actually returned examples
}
```

Summarise from `platforms` alone. `recency_applied: null` means `time_window`
could not be pushed to the provider (Instagram and Threads have no date control,
so their results are filtered locally instead).

**Boolean operators** — uppercase `AND`/`OR`/`NOT`, `subreddit:`, `from:`,
`-exclude`, parentheses — work on twitter, linkedin, linkedin_page and reddit
only. They are stripped elsewhere rather than searched as literal text.

`vs_median` on `get_brand_winning_posts` is the multiple of that brand's own normal
engagement on that platform. Above 1.0 beat the brand's normal; below 1.0
underperformed. It is `null` when the brand has fewer than 3 posts on that platform
— there is no baseline to compare against, so do not invent one.

---

## Posts

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_social_posts` | List posts filtered by type (draft/scheduled/posted) | `type` (required), `workspace_id?`, `created_by?`, `limit?`, `offset?` |
| `get_social_post` | Get complete post details including media, platforms, and logs | `post_id` |
| `create_social_post` | Create a draft or scheduled post with enforced brand/account scope | See below |
| `bulk_create_social_posts` | Create up to 50 drafts or scheduled posts sharing one set of accounts | `posts` (required), `platforms` (required), `brand_id?`, `workspace_id?` |
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

## Review and Rewrite

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `review_social_post` | Run and save the canonical checklist review: anti-AI-slop, factual grounding, brand alignment, platform-native fit, media judgment, CTA, polish — with per-platform pass/fail rationales | `post_id` (required), `brand_id?`, `workspace_id?`, `notes?` |
| `get_social_post_review` | Fetch the latest saved review and whether it is stale against the post's `updated_at` | `post_id` (required), `brand_id?`, `workspace_id?` |
| `rewrite_social_post` | Rewrite using the saved strategy, playbooks, latest review, media policy, and your instructions. Updates drafts in place; published posts get a new draft revision. Never publishes | `post_id` (required), `instructions?`, `brand_id?`, `workspace_id?` |

Writing, review and rewrite are separate executions. If no review exists,
`rewrite_social_post` requires `instructions`. Never call a draft approval-ready
without a fresh passing review of the current content version.

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

Connecting and disconnecting accounts happens in the LotsSocial dashboard, not
through this skill.
