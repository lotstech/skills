# lots.social — Common Workflows

Practical workflow examples for AI agents working on lots.social.

---

## 1. Session Start — Orienting Yourself

Always do this at the start of a session:

```
1. list_workspaces
   → Get workspace_id and your role

2. list_connected_accounts(workspace_id)
   → Know which platforms are available and their account UUIDs

3. list_media(workspace_id)
   → Know what media assets are available
```

---

## 2. Create and Schedule a Post

**Scenario:** Create a post and schedule it for a specific time across multiple platforms.

```
1. list_connected_accounts(workspace_id)
   → Get platform UUIDs (Twitter, Instagram, etc.)

2. list_media(workspace_id)
   → Get media_ids if the post includes images/video

3. create_post(
     caption="Your post text here... #hashtag",
     type="scheduled",
     platforms=["<twitter_account_id>", "<instagram_account_id>"],
     scheduled_time="2026-02-20T10:00:00Z",
     media_ids=["<image_uuid>"],    // optional
     workspace_id="<workspace_id>"
   )
   → Post saved, will auto-publish at scheduled_time
```

**Platform caption limits:**
- Twitter/X: 280 chars
- Instagram: 2,200 chars
- LinkedIn: 3,000 chars
- Facebook: 63,206 chars

---

## 3. Draft → Review → Publish Workflow (Team Approval)

**Scenario:** Agent creates content, manager reviews before publishing.

```
1. create_post(
     caption="...",
     workspace_id="...",
     platforms=["...", "..."]
     // no scheduled_time or type → defaults to "draft"
   )
   → Get post_id

2. add_comment(post_id, "Draft ready for review. Key messages: [...]", workspace_id)
   → Leave context for the reviewer

3. update_approval_status(
     post_id,
     workspace_id,
     action="request_approval",
     comment="Requesting approval for this week's product update post"
   )
   → Status changes to "pending"; manager gets notified

4. [Manager reviews and responds]

5. get_approval_status(post_id, workspace_id)
   → Check if approved or rejected

6a. If approved:
    [Manager or agent publishes] or post gets published per workflow

6b. If rejected:
    list_comments(post_id)  → read rejection reason
    update_post(post_id, caption="...(revised)...")
    update_approval_status(post_id, workspace_id, action="request_approval")
    → Re-request approval after revisions
```

---

## 4. Publish Immediately

**Scenario:** Publish a post right now without drafting.

```
1. list_connected_accounts(workspace_id)
   → Get account UUIDs

2. create_post(
     caption="Breaking: [announcement text]",
     type="posted",
     platforms=["<account_id>"],
     workspace_id="<workspace_id>"
   )
   → Published immediately. No undo — confirm before calling.
```

---

## 5. Analytics Report Workflow

**Scenario:** Generate a performance summary for recent posts.

```
1. get_aggregate_analytics(
     workspace_id,
     date_from="2026-02-01T00:00:00Z",
     date_to="2026-02-19T23:59:59Z"
   )
   → Get total clicks, top posts, platform breakdown

2. list_posts(type="posted", workspace_id)
   → Get recent posted content

3. For top 3-5 posts by engagement:
   get_post_analytics(post_id)
   → Get per-post metrics (likes, comments, shares, impressions)

4. Summarize findings:
   - Best performing post + why
   - Best performing platform
   - Engagement rate trend
   - Recommendations for next period
```

---

## 6. Reschedule a Post

**Scenario:** A scheduled post needs to go out at a different time.

```
1. list_posts(type="scheduled", workspace_id)
   → Find the post to reschedule

2. cancel_scheduled_post(post_id)
   → Reverts to draft status

3. update_post(
     post_id,
     scheduled_time="2026-02-21T14:00:00Z",
     workspace_id
   )
   → Set new time (this changes type back to "scheduled")

   OR use create_post with new scheduled_time if content also needs updating.
```

---

## 7. Account Health Check

**Scenario:** Check all connected accounts for token expiration issues.

```
1. list_connected_accounts(workspace_id)
   → Get all accounts

2. For each account with status != "active" or expiring soon:
   get_account_details(account_id)
   → Check health metrics and expiration

3. For accounts with needs_attention status:
   refresh_account_token(account_id)
   → Attempt token refresh

4. Report any accounts that need manual re-authorization
   (disconnected accounts require OAuth re-connection via web app)
```
