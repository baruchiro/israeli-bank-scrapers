# Automatic bank code owners

When a PR changes a bank's scraper, two workflows fire automatically (both on
`pull_request_target`, so they also work on PRs from forks):

| Workflow | Action | What it does |
|---|---|---|
| `label-banks.yml` | [`actions/labeler`] | Adds a `scraper: <bank>` label based on changed files. |
| `notify-bank-owners.yml` | [`gagoar/use-herald-action`] (Herald) | Comments mentioning that bank's volunteer testers. |

The volunteer list comes from issue **#830 ("Automatic Code Owners")**.

## Adding / changing a volunteer

Edit the bank's rule file in [`.github/herald/`](./herald) — one JSON file per
bank. Add or remove the GitHub handle (no `@`) in the `users` array **and** in
the `customMessage` text:

```json
{
  "name": "Leumi code owners",
  "action": "comment",
  "includes": ["src/scrapers/leumi.ts", "src/scrapers/leumi.test.ts"],
  "users": ["dimdimi4", "idryzhov", "geriamir"],
  "customMessage": "📊 This PR changes the **Leumi** scraper. cc @dimdimi4 @idryzhov @geriamir — ... (see #830)"
}
```

That's the only file to touch for owner changes.

## Adding a new bank

1. Create `.github/herald/<bank>.json` (copy an existing rule, update `includes`,
   `users`, `customMessage`).
2. Add a matching block to [`.github/labeler.yml`](./labeler.yml) so the
   `scraper: <bank>` label is applied too.

## Notes

- **Create the labels first.** The `scraper: <bank>` labels must exist in the
  repo; `actions/labeler` does not create them.
- **Double-check handles.** A wrong username silently pings a stranger.
- Both third-party actions are pinned to commit SHAs in the workflow files;
  bump those SHAs when upgrading.
- Herald's `GITHUB_TOKEN` cannot mention members of **private** organizations
  (not a concern for this public repo).
- These mentions are informational and non-blocking — they don't request review
  or gate merges.

[`actions/labeler`]: https://github.com/actions/labeler
[`gagoar/use-herald-action`]: https://github.com/gagoar/use-herald-action
