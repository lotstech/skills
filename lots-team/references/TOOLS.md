# lots.team — MCP Tools Reference

All tools available via `https://api.lots.team/mcp`. Tools are called by their **Tool Slug**.

---

## Organizations & Team

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_organizations` | List all organizations for the authenticated user | — |
| `get_organization` | Get detailed organization information | `organization_id` |
| `create_organization` | Create a new organization | `name`, `description?` |
| `update_organization` | Update organization details | `organization_id`, `name?`, `description?` |
| `delete_organization` | Delete an organization | `organization_id` |
| `list_organization_members` | List all members with role_instructions for agent context | `organization_id` |
| `update_organization_member` | Update member role, permissions, project access, or role_instructions | `organization_id`, `user_id`, `role?`, `custom_permissions?`, `role_instructions?` |
| `remove_organization_member` | Remove a member from the organization | `organization_id`, `user_id` |
| `list_member_projects` | List project access for a specific member | `organization_id`, `user_id` |
| `set_member_projects` | Replace the full list of project access for a member | `organization_id`, `user_id`, `project_ids` |
| `create_permission_template` | Create a custom permission template for member roles | `organization_id`, `name`, `permissions` |
| `list_permission_templates` | List custom permission templates | `organization_id` |

---

## Invitations

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `create_invitation` | Invite a new member by email | `organization_id`, `email`, `role` |
| `list_invitations` | List organization invitations | `organization_id` |
| `cancel_invitation` | Cancel a pending invitation | `organization_id`, `invitation_id` |
| `resend_invitation` | Resend invitation email | `organization_id`, `invitation_id` |

---

## Projects

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_projects` | List all projects accessible to the authenticated user | — |
| `get_project` | Get detailed project information | `project_id` |
| `create_project` | Create a new project workspace | `organization_id`, `name`, `description?` |
| `update_project` | Update project details | `project_id`, `name?`, `description?` |
| `delete_project` | Delete a project (owner or org delete permission required) | `project_id` |

---

## Tasks

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_tasks` | List all tasks for a project with filtering | `project_id`, `status?`, `assignee_id?`, `priority?` |
| `get_task` | Get detailed information about a specific task | `project_id`, `task_id` |
| `create_task` | Create a new task | `project_id`, `title`, `status?` (todo/in-progress/done), `priority?`, `assignee_id?`, `description?`, `due_date?` |
| `update_task` | Update task details | `project_id`, `task_id`, `title?`, `status?`, `priority?`, `assignee_id?`, `description?` |
| `delete_task` | Permanently delete a task | `project_id`, `task_id` |
| `create_task_comment` | Add a comment to a task | `project_id`, `task_id`, `content` |
| `list_task_comments` | List all comments for a task | `project_id`, `task_id` |
| `update_task_comment` | Update a task comment | `project_id`, `task_id`, `comment_id`, `content` |
| `delete_task_comment` | Delete a task comment | `project_id`, `task_id`, `comment_id` |
| `link_feedback_to_task` | Link a feedback item to a task | `project_id`, `task_id`, `feedback_id` |
| `unlink_feedback_from_task` | Remove link between feedback and task | `project_id`, `task_id`, `feedback_id` |
| `list_linked_feedbacks` | Get all feedback items linked to a specific task | `project_id`, `task_id` |

---

## Feedback

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_feedback` | List feedback items with filtering and sorting | `project_id`, `status?`, `type?`, `search?` |
| `get_feedback` | Get detailed information about a feedback item | `project_id`, `feedback_id` |
| `create_feedback` | Create a new feedback item | `project_id`, `title`, `description?`, `status?`, `type?` |
| `update_feedback` | Update a feedback item | `project_id`, `feedback_id`, `title?`, `description?`, `status?` |
| `delete_feedback` | Permanently delete a feedback item | `project_id`, `feedback_id` |

---

## Customers (CRM)

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_customers` | List all customers with filtering and search | `project_id`, `search?`, `status?` |
| `get_customer` | Get detailed customer information | `project_id`, `customer_id` |
| `create_customer` | Create a new customer record | `project_id`, `name`, `email?`, `company?`, `status?` |
| `update_customer` | Update customer information | `project_id`, `customer_id`, fields to update |
| `delete_customer` | Delete a customer record | `project_id`, `customer_id` |
| `get_customer_stats` | Get aggregated customer statistics | `project_id?`, `organization_id?` |

---

## Changelog

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_changelog` | List changelog entries with filtering | `project_id`, `status?` |
| `get_changelog` | Get a specific changelog entry | `project_id`, `changelog_id` |
| `create_changelog` | Create a new changelog entry | `project_id`, `title`, `content`, `type?` (feature/fix/improvement) |
| `update_changelog` | Update an existing changelog entry | `project_id`, `changelog_id`, `title?`, `content?` |
| `publish_changelog` | Publish a changelog entry to make it visible to users | `project_id`, `changelog_id` |
| `delete_changelog` | Delete a changelog entry | `project_id`, `changelog_id` |

---

## Notes

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_notes` | List all notes for a project (includes author details) | `project_id`, `visibility?` (private/team) |
| `get_note` | Get a specific note | `project_id`, `note_id` |
| `create_note` | Create a new internal note | `project_id`, `title`, `content`, `visibility?` |
| `update_note` | Update a note | `project_id`, `note_id`, `title?`, `content?` |
| `delete_note` | Delete a note | `project_id`, `note_id` |

---

## Contact Messages

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_contact_messages` | List all contact form submissions | `project_id`, `status?` |
| `get_contact_message` | Get a specific contact message | `project_id`, `message_id` |
| `create_contact_message` | Create a new contact form submission | `project_id`, `name`, `email`, `message` |
| `update_contact_message` | Update contact message status | `project_id`, `message_id`, `status` |
| `reply_to_contact_message` | Send a reply to a contact message | `project_id`, `message_id`, `reply_content` |
| `delete_contact_message` | Delete a contact message | `project_id`, `message_id` |

---

## Deliverables

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_deliverables` | List deliverables by task, project, or organization | `task_id?`, `project_id?`, `organization_id?` |
| `get_deliverable` | Get a single deliverable by ID | `deliverable_id` |
| `create_deliverable` | Create a task deliverable (document, design, report, etc.) | `task_id`, `title`, `content`, `type?` |
| `update_deliverable` | Update a deliverable | `deliverable_id`, fields to update |
| `delete_deliverable` | Delete a deliverable | `deliverable_id` |
| `create_deliverable_version` | Create a new version of a deliverable | `deliverable_id`, `content`, `version_notes?` |
| `submit_deliverable_for_review` | Submit a deliverable for human review | `deliverable_id` |
| `approve_deliverable` | Approve a deliverable (reviewer action) | `deliverable_id`, `comment?` |
| `reject_deliverable` | Reject a deliverable with a reason | `deliverable_id`, `reason` |
| `set_primary_deliverable` | Mark a deliverable as the primary output for its task | `deliverable_id` |
| `add_deliverable_comment` | Add a comment to a deliverable | `deliverable_id`, `content` |
| `list_deliverable_comments` | List all comments on a deliverable | `deliverable_id` |
| `resolve_deliverable_comment` | Mark a deliverable comment as resolved/unresolved | `deliverable_id`, `comment_id`, `resolved` |

---

## Knowledge Base

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `lotsteam_list_kb_categories` | List KB categories for an organization | `organization_id` |
| `lotsteam_get_kb_category` | Get a single KB category | `organization_id`, `category_id` |
| `lotsteam_create_kb_category` | Create a new KB category | `organization_id`, `name`, `description?` |
| `lotsteam_update_kb_category` | Update a KB category | `organization_id`, `category_id`, `name?`, `description?` |
| `lotsteam_delete_kb_category` | Delete a KB category | `organization_id`, `category_id` |
| `lotsteam_list_kb_articles` | List KB articles for an organization | `organization_id`, `category_id?`, `status?` |
| `lotsteam_get_kb_article` | Get a single KB article with tags | `organization_id`, `article_id` |
| `lotsteam_create_kb_article` | Create a new KB article | `organization_id`, `category_id`, `title`, `content`, `tags?` |
| `lotsteam_update_kb_article` | Update a KB article | `organization_id`, `article_id`, fields to update |
| `lotsteam_publish_kb_article` | Publish a draft article | `organization_id`, `article_id` |
| `lotsteam_unpublish_kb_article` | Revert a published article to draft | `organization_id`, `article_id` |
| `lotsteam_delete_kb_article` | Delete a KB article | `organization_id`, `article_id` |
| `lotsteam_list_kb_article_versions` | List version history for a KB article | `organization_id`, `article_id` |
| `lotsteam_revert_kb_article_to_version` | Revert a KB article to a previous version | `organization_id`, `article_id`, `version_id` |

---

## Project Context

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_project_context` | List project context items (title + description by default) | `project_id`, `include_content?` |
| `get_project_context_item` | Get full content of a specific context item | `project_id`, `context_item_id` |
| `create_project_context_item` | Create a new project context item | `project_id`, `title`, `description`, `content` |
| `update_project_context_item` | Update a project context item | `project_id`, `context_item_id`, fields to update |
| `delete_project_context_item` | Delete a project context item | `project_id`, `context_item_id` |
| `list_project_context_history` | List version history for a context item | `project_id`, `context_item_id` |
| `revert_project_context_to_version` | Revert a context item to a previous version | `project_id`, `context_item_id`, `version_id` |

---

## Notifications

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_notifications` | List notifications for the current user | `organization_id?`, `project_id?`, `is_read?` |
| `get_notification` | Get a specific notification | `notification_id` |
| `get_unread_notification_count` | Get count of unread notifications | — |
| `create_notification` | Create a notification for a user | `user_id`, `title`, `message`, `type?` |
| `mark_notification_as_read` | Mark a notification as read | `notification_id` |
| `mark_all_notifications_as_read` | Mark all notifications as read for org or project | `organization_id?`, `project_id?` |
| `delete_notification` | Delete a notification | `notification_id` |
| `get_notification_preferences` | Get user notification preferences | — |
| `update_notification_preferences` | Update user notification preferences | preferences object |

---

## Integrations

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `list_integration_providers` | List all available integration providers | — |
| `get_integration_provider` | Get details about a specific provider | `provider_id` |
| `list_project_integrations` | List all integrations connected to a project | `project_id` |
| `connect_integration` | Connect an integration to a project | `project_id`, `provider_slug` |
| `disconnect_integration` | Disconnect an integration from a project | `project_id`, `integration_id` |
