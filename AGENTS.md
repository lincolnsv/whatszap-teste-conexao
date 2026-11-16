<!-- LOVABLE:BEGIN -->
> [!IMPORTANT]
> This project is connected to [Lovable](https://lovable.dev). Avoid rewriting
> published git history — force pushing, or rebasing/amending/squashing commits
> that are already pushed — as it rewrites history on Lovable's side and the
> user will likely lose their project history.
>
> Commits you push to the connected branch sync back to Lovable and show up in
> the editor, so keep the branch in a working state.

## What Lovable manages

Rules outside this block are the project's own. Change the pieces below in
Lovable, not by hand. With the [Lovable MCP](https://mcp.lovable.dev)
connected, `send_message` asks Lovable to do it; otherwise ask the user to
use the editor.

- **Database.** Do not add or edit `supabase/migrations/` or `drizzle/`.
  Ask Lovable to change the schema.
- **Generated files.** Do not edit a file whose header says it is automatically
  generated.
- **AI gateway.** Keep model calls on Lovable's gateway, and keep
  `LOVABLE_API_KEY` out of browser code.
- **Connectors.** Lovable provides native connectors. `list_connectors` shows
  which are enabled; `add_connector` only returns the dashboard link. When a
  connector is enabled, calls go through the connector gateway and need
  `LOVABLE_API_KEY`. When Lovable does not provide one, users can bring their
  own key and store it in Secrets.
- **Secrets.** Lovable manages them. Do not put secret values in the repo.
<!-- LOVABLE:END -->

## Account isolation
- Scope application rows and query-cache keys to the authenticated user's UUID; RLS has no admin bypass, preventing buyers from sharing data.
- Use `account_settings` and `account_plans` for per-account configuration; legacy singleton tables are deprecated and owner-restricted.
- Validate immutable ownership and same-owner client/server/plan links in database triggers; privileged reminder sends require an explicit owner and use that owner's settings.
- Buyer provisioning is separate from data isolation; never grant buyers the platform admin role just to access their own rows.
- Platform admin server functions verify `has_role` with the caller's RLS client before privileged reseller provisioning; profiles hold contact data, while protected reseller accounts control access and RLS blocks disabled accounts.
- Enforce reseller expiration through `account_access_enabled` using the Brasilia calendar date; poll owner-scoped access in the shell to hide cached data, and keep manual blocking independent from validity extensions.
