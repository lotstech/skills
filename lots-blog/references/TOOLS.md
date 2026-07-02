# lots.blog — MCP Tools Reference

All tools available via `https://api.lots.blog/mcp`. Tools are called by their **Tool Slug**.

---

## Blogs

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_blogs` | List all blogs where user is an owner or active member | — |
| `get_blog` | Get comprehensive blog details, settings, SSL status, and user role | `blog_id` |
| `get_blog_settings` | Alias for `get_blog` emphasizing settings view | `blog_id` |
| `create_blog` | Create a new blog instance | `blog_name` (required), `subdomain` (required, unique, 3-63 chars), `privacy?` (public/private) |
| `update_blog` | Update blog settings, theme, subdomain (Owner), or custom domain | `blog_id`, fields to update |
| `delete_blog` | Permanently delete a blog (Owner only; no published posts allowed) | `blog_id` |

---

## Posts

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_posts` | List posts with optional filtering by type, status, topic | `blog_id`, `type?`, `status?`, `topic_id?`, `limit?` |
| `get_post` | Get full post details including type-specific content | `blog_id`, `post_id` |
| `create_post` | Create a new post | See below |
| `update_post` | Update post content, metadata, or status | `blog_id` (required), `post_id` (required), fields to update |
| `publish_post` | Immediately publish a draft or scheduled post (Editor+ only) | `blog_id`, `post_id` |
| `schedule_post` | Schedule a post for automatic publishing | `blog_id`, `post_id`, `scheduled_for` (ISO 8601, 5+ min ahead) |
| `delete_post` | Permanently delete a post (Owner/Admin only) | `blog_id`, `post_id` |

---

## Autopilot, Strategy, and Production Queue

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_blog_strategy` | Load blog identity, source brief, pillars, keyword clusters, content ideas, briefs, and recent posts | `blog_id` |
| `save_blog_settings` | Save blog foundation: purpose, reader, voice, territory, cadence, CTA, notes | `blog_id`, fields to update |
| `save_blog_strategy` | Save full strategy/source brief context | `blog_id`, strategy fields |
| `find_keyword_ideas` | Research and save keyword ideas from seed phrases | `blog_id`, `seeds`, `limit?` |
| `list_keywords` | List stored keyword metrics and usage | `blog_id`, filters? |
| `group_keywords` | Group keywords into a cluster | `blog_id`, `keyword_ids`, `name?`, `cluster_id?` |
| `create_content_idea` | Save an idea with status, pillar, keyword, production timing, and reasoning | `blog_id`, `title`, `status?`, `production_status?` |
| `update_content_idea` | Update an idea, approval status, timing, or production state | `blog_id`, `content_idea_id`, fields |
| `update_idea_approval` | Approve or reject one or more content ideas | `blog_id`, `content_idea_ids`, `action` |
| `list_content_ideas` | List ideas by status or production state | `blog_id`, filters? |
| `create_post_brief` | Create a writing brief for an idea | `blog_id`, `content_idea_id`, brief fields |
| `update_post_brief` | Edit a writing brief | `blog_id`, `brief_id`, fields |
| `get_production_queue` | Inspect ideas, briefs, due drafts, draft-ready items, scheduled posts, and blockers | `blog_id` |
| `update_production_status` | Move an idea through idea/brief/drafting/draft_ready/reviewing/scheduled/published states | `blog_id`, `content_idea_id`, statuses |
| `save_content_review` | Save AI review score, findings, readiness status, and the dashboard-visible post quality check | `blog_id`, `post_id`, review fields |
| `get_content_reviews` | Retrieve saved reviews for a post | `blog_id`, `post_id` |
| `get_autopilot_settings` | Read autopilot enabled state, cadence, timezone, approval modes, quality score, buffers, notifications | `blog_id` |
| `update_autopilot_settings` | Change autopilot settings | `blog_id`, settings fields |
| `get_weekly_cadence_status` | Check Monday-Sunday cadence in blog timezone | `blog_id`, `timezone?` |

Cadence rule: always use `get_weekly_cadence_status` before maintaining buffers. Approval rule: in approval-required mode, notify/escalate instead of proceeding.

### `create_post` Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `blog_id` | ✅ | UUID of the target blog |
| `content_idea_id` | — | Strongly recommended when drafting from a saved content idea or post brief. Links the new post to the idea atomically and prevents duplicate drafts for the same idea. |
| `title` | ✅ | Post title (all types) |
| `post_type` | ✅ | `article`, `list`, `poll`, `video`, or `note` |
| `status` | — | `draft` (default), `scheduled`, `published` |
| `content` | Article only | Markdown content |
| `note` | Note only | Short text (max 1000 chars, markdown) |
| `youtube_link` | Video only | YouTube URL |
| `question` | Poll only | Poll question |
| `option1`, `option2` | Poll only | First two poll options (required); `option3`, `option4` optional |
| `list_items` | List only | Array of `{title, order_index, description?, image_url?}` |
| `scheduled_for` | If status=scheduled | ISO 8601 datetime, 5+ min in future |
| `featured_image` | — | URL to featured image |
| `meta_description` | — | SEO description (max 160 chars) |
| `meta_keywords` | — | Array of SEO keywords |
| `slug` | — | Custom URL slug (auto-generated from title if omitted) |
| `reading_time` | Article only | Estimated reading time in minutes |
| `source_link` | — | Original source URL for attribution |

---

## Topics (Categories)

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_topics` | List topics with optional parent filtering and post counts | `blog_id`, `parent_id?`, `include_post_count?` |
| `get_topic` | Get topic details including parent and child topics | `blog_id`, `topic_id` |
| `create_topic` | Create a new topic/category (Editor+ only) | `blog_id`, `name`, `description?`, `parent_id?`, `seo_title?`, `seo_description?` |
| `update_topic` | Update topic details (Editor+ only) | `blog_id`, `topic_id`, fields to update |
| `delete_topic` | Delete a topic (must have no children; reassign posts first) | `blog_id`, `topic_id` |

---

## Comments

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_comments` | List comments with user info, reactions, reply counts | `blog_id`, `post_id`, `include_hidden?` |
| `create_comment` | Create a comment or threaded reply | `blog_id`, `post_id`, `content` (1-1000 chars), `parent_comment_id?` |
| `update_comment` | Edit comment (author only) | `blog_id`, `comment_id`, `content` |
| `delete_comment` | Delete a comment (author or Owner/Admin) | `blog_id`, `comment_id` |
| `toggle_comment_visibility` | Hide or unhide a comment for moderation (Owner/Admin only) | `blog_id`, `comment_id` |

---

## Analytics

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `get_blog_analytics` | Blog-level stats: total views, posts, comments, likes. Views by date, top 10 posts | `blog_id`, `date_from?`, `date_to?` (default: last 30 days) |
| `get_post_analytics` | Post engagement: total views, likes, comments, saves | `blog_id`, `post_id` |

---

## Team Management

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_blog_users` | List blog members with roles, status, pagination | `blog_id`, `role?`, `status?`, `limit?`, `offset?` |
| `get_blog_user` | Get user details including post count and activity stats | `blog_id`, `user_id` |
| `invite_user` | Create an invitation token for a new user | `blog_id`, `email`, `role` (not owner), `expires_in_days?` (max 30) |
| `remove_user` | Remove a member from the blog | `blog_id`, `user_id` |
| `update_user_role` | Change a user's role | `blog_id`, `user_id`, `role` |
| `list_pending_invitations` | List all active, unused invitations | `blog_id` |
| `revoke_invitation` | Cancel a pending invitation | `blog_id`, `invitation_id` |

### Roles (lowest to highest)

`user` → `author` → `editor` → `admin` → `owner`

| Role | Can Do |
|------|--------|
| `user` | Read + comment |
| `author` | Create/edit own posts (cannot publish) |
| `editor` | Create/edit/publish/schedule any post; manage topics |
| `admin` | All editor actions + team management + most blog settings |
| `owner` | Everything + subdomain/domain changes + blog deletion |
