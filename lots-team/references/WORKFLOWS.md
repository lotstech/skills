# lots.team — Common Workflows

Practical workflow examples for AI agents working on lots.team.

---

## 1. Session Start — Orienting Yourself

Always do this at the start of a session before taking any action:

```
1. lotsteam_list_organizations         → get organization_id
2. lotsteam_list_organization_members  → find your member record, read role_instructions
3. lotsteam_list_projects              → get available project_ids
4. lotsteam_list_project_context       → skim titles/descriptions for project context
```

If your member record has `role_instructions`, read and follow them — the human team wrote them specifically for you.

---

## 2. Feedback Triage Workflow

**Scenario:** Review new feedback, categorize it, create tasks for actionable items, link them.

```
1. lotsteam_list_feedback(project_id, status="new")
   → Identify feedback to triage

2. For each feedback item:
   a. lotsteam_get_feedback(project_id, feedback_id)
      → Read full description

   b. lotsteam_update_feedback(project_id, feedback_id, status="triaged", type="bug|feature|improvement")
      → Categorize it

   c. If actionable:
      lotsteam_create_task(project_id, title="...", status="todo", priority="medium|high")
      → Get task_id from response

      lotsteam_link_feedback_to_task(project_id, task_id, feedback_id)
      → Connect feedback to task

3. lotsteam_create_notification(user_id=<team_lead>, title="Feedback triage complete", message="X items triaged, Y tasks created")
   → Alert the team
```

---

## 3. Task Completion Workflow (with Deliverable)

**Scenario:** Pick up an assigned task, do the work, create a deliverable, submit for review.

```
1. lotsteam_list_tasks(project_id, status="in-progress", assignee_id=<my_user_id>)
   → Find my assigned tasks

2. lotsteam_get_task(project_id, task_id)
   → Understand requirements

3. lotsteam_list_project_context(project_id)
   → Get relevant project context and guidelines

4. [Do the work — write content, analysis, etc.]

5. lotsteam_create_deliverable(task_id, title="...", content="<markdown content>", type="document")
   → Create the deliverable

6. lotsteam_update_task(project_id, task_id, status="in-progress")
   → Confirm task is active

7. lotsteam_submit_deliverable_for_review(deliverable_id)
   → Send to human for review. DO NOT self-approve.

8. lotsteam_create_notification(user_id=<task_owner>, title="Deliverable ready for review", message="...")
   → Alert the reviewer
```

---

## 4. Changelog Publishing Workflow

**Scenario:** Draft and publish a product update changelog entry.

```
1. lotsteam_list_tasks(project_id, status="done")
   → Find recently completed tasks to highlight

2. lotsteam_list_feedback(project_id, status="resolved")
   → Find resolved feedback to mention

3. lotsteam_create_changelog(
     project_id,
     title="v1.x — [Feature Name]",
     content="## What's New\n\n...\n\n## Bug Fixes\n\n...",
     type="feature"
   )
   → Get changelog_id

4. [Wait for human review / approval]

5. lotsteam_publish_changelog(project_id, changelog_id)
   → Make it visible to users
```

---

## 5. Knowledge Base Article Workflow

**Scenario:** Create a KB article for a new feature or process.

```
1. lotsteam_list_kb_categories(organization_id)
   → Find the right category

2. lotsteam_create_kb_article(
     organization_id,
     category_id,
     title="How to...",
     content="<full markdown content>",
     tags=["feature", "guide"]
   )
   → Create as draft

3. [Human reviews the draft]

4. lotsteam_publish_kb_article(organization_id, article_id)
   → Publish to make visible
```

---

## 6. Customer CRM Update Workflow

**Scenario:** Sync customer records after an integration import or manual update.

```
1. lotsteam_list_customers(project_id, search="<company>")
   → Check if customer exists

2a. If exists:
    lotsteam_get_customer(project_id, customer_id)
    → Review current data

    lotsteam_update_customer(project_id, customer_id, company="...", status="active")
    → Update record

2b. If not exists:
    lotsteam_create_customer(project_id, name="...", email="...", company="...", status="active")
    → Create new record

3. lotsteam_get_customer_stats(project_id)
   → Verify aggregate stats
```

---

## 7. Contact Message Response Workflow

**Scenario:** Process inbound support messages. Contact messages support **threaded conversations** — customers can reply back via the support portal, so you may need to handle follow-ups.

```
1. lotsteam_list_organization_members(organization_id)
   → Check if a "Customer Support Manager" exists on the team

2. If a support manager exists AND you are NOT the support manager:
   → Delegate: lotsteam_create_notification(
       user_id=<support_manager_user_id>,
       title="New contact message needs handling",
       message="A new support message has arrived. Please review and respond."
     )
   → STOP — do not handle the message yourself

3. If you ARE the support manager (or none exists), proceed:

4. lotsteam_list_contact_messages(project_id, status="new")
   → Find unhandled messages

5. lotsteam_get_contact_thread(message_id)
   → Read the FULL thread (original message + all replies)
   → This is critical for follow-ups — the customer may have replied with more details

6. [Determine appropriate response based on the full conversation history]

7. lotsteam_reply_to_contact_message(contact_message_id, reply_message="...")
   → Send reply (email with "View & Reply" link is sent automatically)
   → Multiple replies are supported — threads can go back and forth

8. lotsteam_update_contact_message(message_id, status="read")
   → Mark as handled (status resets to "new" if customer replies again)
```

**Important:** Always use `get_contact_thread` instead of `get_contact_message` — the thread view shows the full conversation history including customer follow-ups. A message with status "new" may be a follow-up reply, not a first-time message.

---

## 8. Permission Check Pattern

Before performing a sensitive action, verify you have the right permissions:

```
1. lotsteam_list_organization_members(organization_id)
   → Find your member record

2. Check member.role:
   - "owner" or "admin" → proceed with any action
   - "viewer" → read-only, no mutations
   - "member" → check member.custom_permissions for specific permission keys

3. Check member.role_instructions for any additional constraints
```
