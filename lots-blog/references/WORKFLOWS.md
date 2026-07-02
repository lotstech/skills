# lots.blog — Common Workflows

Practical workflow examples for AI agents working on lots.blog.

---

## 1. Session Start — Orienting Yourself

Always do this at the start of a session:

```
1. list_blogs
   → Get blog_id and your role in each blog

2. get_blog_strategy(blog_id)
   → Load identity, source brief, pillars, keywords, ideas, briefs, and production context

3. get_autopilot_settings(blog_id)
   → Understand cadence, timezone, approval modes, quality score, buffers, and notifications

4. get_weekly_cadence_status(blog_id)
   → Check the current Monday-Sunday week before doing any buffer maintenance

5. get_production_queue(blog_id)
   → Inspect ideas, briefs, drafts, scheduled posts, and blockers
```

---

## 2. Cadence-First Autopilot Workflow

**Scenario:** Keep the blog honest to its weekly publishing promise.

```
1. get_autopilot_settings(blog_id)
2. get_weekly_cadence_status(blog_id)

3. If strategy is missing:
   notify/escalate owner for the exact missing strategy inputs.
   Do not ask questions in hidden/autopilot chat.

4. If current week is behind:
   a. Use existing draft-ready posts first.
   b. If drafts need review, run quality review/rewrite until quality_pass_score is met. Every review must be persisted with `save_content_review`; do not count a draft as reviewed unless the tool returns a dashboard `quality_check`.
   c. If post_approval_mode="approval_required", notify owner with ready drafts and schedule/publish choices.
   d. If post_approval_mode="auto", publish/schedule to cover the current week.
      - Early week: schedule remaining cadence across the week.
      - Mid/late week with no published posts: publish one now if appropriate, schedule the rest.
   e. If no drafts exist, create drafts from ready briefs.
   f. If no briefs exist, create briefs from approved ideas.
   g. If idea_approval_mode="approval_required" and only pending ideas exist, notify owner with numbered idea choices.
   h. If no usable ideas exist, generate strong ideas from strategy, keyword gaps, posts, and external research where needed.

5. Only after current-week cadence is healthy:
   maintain keyword, idea, brief, draft, review, refresh, and performance buffers.
```

Autopilot sessions are unattended. Never call hidden user-input tools and never ask a question in chat. Use email/Telegram/dashboard notification paths for approvals or missing inputs.

---

## 3. Write and Publish an Article

**Scenario:** Create a full article, optimize for SEO, and publish.

```
1. list_topics(blog_id)
   → Find the right topic/category for this article

2. create_post(
     blog_id,
     content_idea_id=<source_content_idea_id_if_using_saved_idea_or_brief>,
     title="How to [Topic]: A Complete Guide",
     post_type="article",
     content="# Introduction\n\n...\n\n## Section 1\n\n...",
     status="draft",
     featured_image="https://...",
     meta_description="Learn how to [topic] with our step-by-step guide. Covers [key points].",
     meta_keywords=["keyword1", "keyword2"],
     reading_time=5
   )
   → Get post_id

   When drafting from a saved content idea or post brief, always pass `content_idea_id` in the create call. This links the draft back to the idea immediately and removes it from the brief-ready queue. If the tool reports that the idea already has a linked post, use the existing draft instead of creating another.

3. [Human reviews draft]

4. publish_post(blog_id, post_id)
   → Make live immediately

   OR schedule_post(blog_id, post_id, scheduled_for="2026-02-25T09:00:00Z")
   → Schedule for specific time
```

**Article content tips:**
- Use proper Markdown headings (`#`, `##`, `###`)
- Include code blocks with language hints (` ```javascript `)
- Keep `meta_description` to 120-160 characters for best SEO
- Always set `featured_image` for social sharing previews

---

## 4. Create a Listicle Post

**Scenario:** Create a "Top 10" style ranked list post.

```
1. create_post(
     blog_id,
     title="Top 10 Tools for [Topic] in 2026",
     post_type="list",
     status="draft",
     list_items=[
       {"title": "Tool Name 1", "description": "Why it's great...", "order_index": 0, "image_url": "https://..."},
       {"title": "Tool Name 2", "description": "...", "order_index": 1},
       // ... up to 10 items
     ],
     featured_image="https://...",
     meta_description="Our picks for the best [topic] tools this year."
   )
```

---

## 5. Editorial Calendar Workflow

**Scenario:** Schedule a batch of posts for the coming week.

```
1. list_posts(blog_id, status="draft")
   → Find all ready-to-schedule drafts

2. For each draft to schedule:
   get_post(blog_id, post_id)
   → Verify content is complete (has featured_image, meta_description, topic)

   schedule_post(blog_id, post_id, scheduled_for="2026-02-21T09:00:00Z")
   → Schedule at staggered times (e.g., Mon/Wed/Fri at 9 AM)

3. list_posts(blog_id, status="scheduled")
   → Confirm the schedule looks correct
```

---

## 6. Comment Moderation Workflow

**Scenario:** Review and moderate comments on recent posts.

```
1. list_posts(blog_id, status="published", limit=5)
   → Get recent posts

2. For each post:
   list_comments(blog_id, post_id)
   → Review all comments

3. For inappropriate comments:
   toggle_comment_visibility(blog_id, comment_id)
   → Hide the comment (reversible)

   OR delete_comment(blog_id, comment_id)
   → Permanently remove

4. For comments that need a response:
   create_comment(blog_id, post_id, content="Thank you for...", parent_comment_id=<comment_id>)
   → Reply to the commenter
```

---

## 7. Blog Analytics Review

**Scenario:** Generate a monthly performance report.

```
1. get_blog_analytics(
     blog_id,
     date_from="2026-02-01T00:00:00Z",
     date_to="2026-02-28T23:59:59Z"
   )
   → Total views, comments, likes; top 10 posts; views by date

2. For the top 3 performing posts:
   get_post_analytics(blog_id, post_id)
   → Views, likes, comments, saves breakdown

3. list_posts(blog_id, status="published", limit=20)
   → Find posts with no analytics (potential distribution issue)

4. Summarize:
   - Views trend (growing/declining)
   - Top content categories
   - Best publishing times
   - Recommendations
```

---

## 8. Invite a New Team Member

**Scenario:** Onboard a new author to the blog.

```
1. list_blog_users(blog_id)
   → Check if user is already a member

2. invite_user(
     blog_id,
     email="newauthor@example.com",
     role="author",
     expires_in_days=7
   )
   → Invitation link is generated and emailed

3. list_pending_invitations(blog_id)
   → Confirm invitation was created

4. [If invitation expires before user joins:]
   revoke_invitation(blog_id, invitation_id)
   → Cancel old invitation
   invite_user(...)
   → Re-invite
```
