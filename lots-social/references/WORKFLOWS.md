# lots.social — Common Workflows

Practical workflow examples for AI agents working on lots.social.

---

## 1. Session Start — Select One Brand

When the request does not already supply a Brand ID:

```
1. list_workspaces
   → Get workspace_id and your role

2. list_connected_accounts(workspace_id)
   → Resolve the accounts for exactly one selected brand

3. Keep brand_id + allowed account IDs immutable for the operation
```

---

## 2. Normal Teammate Run

**Scenario:** The owner wants the teammate to plan and prepare the next useful posts.

```
run_social_teammate_workflow(
  brand_id="<selected_brand_id>",
  workspace_id="<workspace_id>",
  source="agent",
  idempotency_key="<stable-request-key>"
)
→ Returns scoped outcome counts and exact posts/questions needing attention.
```

This is the default. It performs bounded planning, writing, independent review,
at most two rewrites, approval readiness, and eligible cadence scheduling.

---

## 3. Explicit One-Post Edit

**Scenario:** The owner explicitly asks to create or edit one artifact outside the
normal teammate cycle.

```
1. list_connected_accounts(workspace_id)
   → Get platform UUIDs (Twitter, Instagram, etc.)

2. list_media(workspace_id)
   → Get media_ids if the post includes images/video

3. create_post(
     caption="Your post text here... #hashtag",
     type="draft",
     brand_id="<selected_brand_id>",
     platforms=["<twitter_account_id>", "<instagram_account_id>"],
     media_ids=["<image_uuid>"],    // optional
     workspace_id="<workspace_id>"
   )
   → Draft saved. Run review_social_post before requesting approval.
```

**Platform caption limits:**
- Twitter/X: 280 chars
- Instagram: 2,200 chars
- LinkedIn: 3,000 chars
- Facebook: 63,206 chars

---

## 4. Draft → Independent Review → Approval

**Scenario:** Agent creates content, manager reviews before publishing.

```
1. create_post(
     caption="...",
     workspace_id="...",
     platforms=["...", "..."]
     // no scheduled_time or type → defaults to "draft"
   )
   → Get post_id

2. review_social_post(post_id, brand_id, workspace_id)
   → Fresh independent score and checklist

3. If it fails: rewrite_social_post(post_id, brand_id, workspace_id)
   → Re-review. Stop after two failed automatic rewrites.

4. Only after a fresh pass: set_social_post_approval(
     post_id,
     workspace_id,
     brand_id,
     action="request_approval",
     comment="Requesting approval for this week's product update post"
   )
   → Status changes to "pending"; manager gets notified

5. [Manager reviews and responds]

6. get_approval_status(post_id, workspace_id)
   → Check if approved or rejected

7a. If approved:
    run_social_teammate_workflow(brand_id, workspace_id)
    → Code chooses a valid cadence slot when settings allow scheduling.

6b. If rejected:
    list_comments(post_id)  → read rejection reason
    update_post(post_id, caption="...(revised)...")
    update_approval_status(post_id, workspace_id, action="request_approval")
    → Re-request approval after revisions
```

---

## 5. Publish Immediately

MCP post creation cannot fake a published state. Create a draft, pass review, obtain
approval, and use LotsSocial’s validated publishing UI/pipeline for “publish now.”

---

## 6. Analytics Report Workflow

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

## 7. Reschedule a Post

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

## 8. Account Health Check

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
