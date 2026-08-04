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

4. get_business_profile(brand_id) and get_brand_social_goal(brand_id)
   → Read the saved context before asking the owner anything.
```

---

## 2. Write This Week's Posts

**Scenario:** the owner asks for posts about something specific.

```
1. get_business_profile(brand_id)
   → Public truth is the only source of stateable facts.
   → Internal guidance steers voice; never quote it as fact.

2. get_social_campaign(brand_id)          [optional]
   → If one is active, use its goal and per-account notes as context.
   → If none is active, write anyway. A campaign is not required.

3. For each target account:
   get_platform_playbook(platform)
   → Obey critical_rules, writing_structure, length_and_media, link policy.

4. list_media(workspace_id, search)       [when a post needs an asset]
   → Reuse an existing screenshot or photo before asking for a new one.

5. create_social_post(brand_id, platforms, caption or platform_captions, media_ids?)
   → One call can target several accounts with per-account captions.

6. review_social_post(post_id, brand_id)
   → Report what passed and what needs fixing.

7. Report back: what you drafted, for which accounts, and what needs the owner.
   Stop there. Do not schedule or approve unless asked.
```

If a post needs a fact you do not have — this week's result, a customer win, a
screenshot of something new — ask one concise question and write the posts that
do not depend on it.

---

## 3. Explicit One-Post Edit

**Scenario:** The owner explicitly asks to create or edit one specific post.

```
1. list_connected_accounts(workspace_id)
   → Get platform UUIDs (Twitter, Instagram, etc.)

2. list_media(workspace_id)
   → Get media_ids if the post includes images/video

3. create_social_post(
     caption="Your post text here... #hashtag",
     type="draft",
     brand_id="<selected_brand_id>",
     standalone=true,
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
1. create_social_post(
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
    update_social_post(post_id, scheduled_time="2026-08-12T09:00:00Z")
    → Sets the time and queues the job. Ask the owner for the slot;
      do not choose one on their behalf.

6b. If rejected:
    list_comments(post_id)  → read rejection reason
    update_social_post(post_id, caption="...(revised)...")
    set_social_post_approval(post_id, workspace_id, action="request_approval")
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

2. list_social_posts(type="posted", workspace_id)
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
1. list_social_posts(type="scheduled", workspace_id)
   → Find the post to reschedule

2. cancel_scheduled_post(post_id)
   → Reverts to draft status

3. update_social_post(
     post_id,
     scheduled_time="2026-02-21T14:00:00Z",
     workspace_id
   )
   → Set new time (this changes type back to "scheduled")

   OR use create_social_post with new scheduled_time if content also needs updating.
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

3. Report accounts that need attention. Token refresh is automatic where the
   provider supports it; accounts requiring re-authorization must be reconnected
   through the LotsSocial web app.
```

---

## 9. Reddit Draft Checklist (fail closed)

**Scenario:** Owner wants a Reddit post, comment, or “promotion” for a connected Reddit account.

Reddit is **not** Instagram, LinkedIn, X, or a brand broadcast channel. Each
subreddit is its own moderated community. Prefer the attached campaign-plan
playbook snapshot when present; otherwise call `get_platform_playbook`.

```
1. get_platform_playbook(platform="reddit")
   → Read critical_rules, agent_caution, writing_structure, link_policy.
   → Obey fail-closed rules before drafting.

2. Confirm target context (required before any promo-shaped draft):
   - Which subreddit?
   - What do that sub’s rules say about self-promotion, flair, title format,
     link posts, images, NSFW/spoiler?
   - Is this a text post, link post, image/gallery, or a comment on an
     existing thread?
   If any of these are unknown → stop. Ask the owner, or write value-only
   help with zero product pitch. Do not invent that promo is allowed.

3. Tone and structure test:
   - Title: plain, specific, honest — no launch-hype, no hashtag walls.
   - Body: problem/result/question first for the community; never open with
     a company pitch.
   - Brand mention: only if rules allow and value stands alone without it;
     disclose affiliation when the product is mentioned.
   - Links: high-risk. Omit unless rules clearly allow and the post already
     helps without the click.
   - Hashtags: do not use.

4. Self-check before create_social_post / rewrite:
   - Would a skeptical moderator call this an ad? If yes → rewrite or kill
     the brand mention.
   - Would this work as a helpful post from a real community member with a
     website (not a website with a Reddit account)? If no → rewrite.
   - Prefer answering an existing thread over starting a promo post when
     the owner’s goal is trust or discovery.

5. create_social_post / review_social_post / rewrite_social_post
   → Use the Reddit account UUID in platforms.
   → Review against the Reddit playbook (attached version or live tool).
   → Never reuse LinkedIn/X/Instagram caption drafts on Reddit.
```

**Hard stops (do not post):**
- Subreddit unknown
- Self-promo banned or unclear
- Request is “promote our product” with no community value and no rule check
- Astroturf, fake “as a user” voice, sockpuppet, or vote-manipulation framing
