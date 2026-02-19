# lots.link — MCP Tools Reference

All tools available via `https://api.lots.link/mcp`. Tools are called by their **Tool Slug**.
Note: All lots.link tool slugs are prefixed with `lotslink_`.

---

## Workspaces

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `lotslink_list_workspaces` | List all workspaces where user is an active member | — |
| `lotslink_get_workspace` | Get workspace details, settings, user role, member/link counts | `workspace_id` |
| `lotslink_create_workspace` | Create a new workspace | `name` (required), `description?`, `settings?` |
| `lotslink_update_workspace` | Update workspace name, description, or settings (Owner/Admin) | `workspace_id`, `name?`, `description?` |
| `lotslink_delete_workspace` | Permanently delete a workspace and all its data (Owner only) | `workspace_id` |

---

## Links

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `lotslink_list_links` | List links with filtering, search, and pagination | `workspace_id` (required), `project_id?`, `status?` (all/active/inactive/expired), `search?`, `sort_by?`, `limit?`, `page?` |
| `lotslink_get_link` | Get link details including metadata, UTM params, and click stats | `workspace_id`, `link_id` |
| `lotslink_create_link` | Create a shortened link | See below |
| `lotslink_update_link` | Update link properties | `workspace_id` (required), `link_id` (required), `custom_slug?`, `is_active?`, `expires_at?`, `project_id?` |
| `lotslink_delete_link` | Permanently delete a link (own links; Owner/Admin for any) | `workspace_id`, `link_id` |
| `lotslink_bulk_activate_links` | Activate multiple links at once (Owner/Admin only) | `workspace_id`, `link_ids` (array) |
| `lotslink_bulk_deactivate_links` | Deactivate multiple links at once (Owner/Admin only) | `workspace_id`, `link_ids` (array) |
| `lotslink_bulk_delete_links` | Permanently delete multiple links (Owner/Admin only) | `workspace_id`, `link_ids` (array) |

### `lotslink_create_link` Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `workspace_id` | ✅ | Target workspace UUID |
| `original_url` | ✅ | Full URL to shorten (http/https) |
| `custom_slug` | — | Custom slug (7-40 chars, alphanumeric + `-` + `_`); auto-generated if omitted |
| `project_id` | — | Assign to a project UUID |
| `expires_at` | — | ISO 8601 expiration datetime |
| `utm_source` | — | UTM source (e.g., `newsletter`, `twitter`) |
| `utm_medium` | — | UTM medium (e.g., `email`, `social`, `cpc`) |
| `utm_campaign` | — | UTM campaign name |
| `utm_term` | — | UTM term (paid search keywords) |
| `utm_content` | — | UTM content (A/B variant identifier) |
| `tag_ids` | — | Array of tag UUIDs for categorization |

---

## Projects

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `lotslink_list_projects` | List all projects with link counts | `workspace_id`, `include_archived?` |
| `lotslink_get_project` | Get project details and link count | `workspace_id`, `project_id` |
| `lotslink_create_project` | Create a project for organizing links | `workspace_id`, `name`, `description?`, `color?`, `client_info?` |
| `lotslink_update_project` | Update project details | `workspace_id`, `project_id`, `name?`, `description?`, `color?` |
| `lotslink_archive_project` | Archive a project (soft delete; links remain accessible) | `workspace_id`, `project_id` |
| `lotslink_delete_project` | Permanently delete a project (no active links allowed) | `workspace_id`, `project_id` |

---

## Custom Domains

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `lotslink_list_custom_domains` | List all custom domains for a workspace | `workspace_id`, `status?` |
| `lotslink_get_custom_domain` | Get domain details, DNS records, SSL status, verification status | `workspace_id`, `domain_id` |
| `lotslink_add_custom_domain` | Add a custom domain or *.lots.link subdomain | `workspace_id`, `domain` |
| `lotslink_verify_custom_domain` | Trigger DNS verification (checks for correct DNS records) | `workspace_id`, `domain_id` |
| `lotslink_update_custom_domain` | Activate or deactivate a custom domain | `workspace_id`, `domain_id`, `is_active` |
| `lotslink_delete_custom_domain` | Delete a custom domain (no active links allowed) | `workspace_id`, `domain_id` |

> **Domain flow:** Add domain → Configure DNS records (from `get_custom_domain`) → Verify → SSL auto-provisions

---

## Analytics

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `lotslink_get_link_analytics` | Detailed link analytics: geo, devices, referrers, unique/total clicks, daily timeline | `workspace_id` (required), `link_id` (required), `days?` (1-365, default 30), `timezone?` |
| `lotslink_get_workspace_analytics` | Workspace-level overview: total clicks, active links, growth, top performers | `workspace_id`, `days?` |
| `lotslink_get_click_details` | Paginated individual click records with IP, geo, device, referrer | `workspace_id`, `link_id`, `filters?`, `limit?`, `offset?` |
| `lotslink_export_analytics` | Export click data as JSON or CSV (max 10,000 records) | `workspace_id`, `link_id?` (omit for all links), `format?` (json/csv) |

---

## Team

| Tool Slug | Description | Key Parameters |
|-----------|-------------|----------------|
| `lotslink_invite_workspace_member` | Invite a user to workspace by email (Owner/Admin; quota enforced) | `workspace_id`, `email`, `role` |
| `lotslink_remove_workspace_member` | Remove a member from workspace (Owner/Admin; cannot remove owner) | `workspace_id`, `user_id` |
