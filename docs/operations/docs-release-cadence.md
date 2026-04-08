# Docs Release Cadence and Publishing

This runbook defines how Leanmote documentation changes are validated, released, and rolled back.

## Weekly release cadence

- Primary cadence: every Wednesday at 16:00 UTC.
- Release owner: Docs Engineer on duty.
- Scope for each release:
  - merged content updates from `main`
  - API reference updates
  - navigation/content quality fixes found during review

## CI quality gates

Every pull request and push to `main` must pass the docs quality workflow:

- `mkdocs build --strict` to fail on warnings and broken references.
- offline link integrity checks across docs markdown and MkDocs config.

Only changes that pass these checks should be merged.

## Publish workflow

Publishing to GitHub Pages is fully automated:

1. Open a PR and wait for the docs quality workflow to pass.
2. Merge into `main`.
3. GitHub Actions runs the docs deploy workflow (`Deploy docs`).
4. The build artifact is deployed to the `github-pages` environment.
5. Validate key pages after deploy:
   - home page
   - changed page(s)
   - search index behavior

## Rollback notes

If a bad docs release reaches production:

1. Identify the last known good commit on `main`.
2. Revert the bad commit(s) with a new commit (do not force-push).
3. Push the revert commit to `main`.
4. Confirm the deploy workflow completes successfully.
5. Verify the site reflects the rollback and log the incident in the issue.

## Ownership checklist

- Keep the weekly release cadence unless a hotfix is required.
- Keep CI workflows green before merge.
- Record release-impacting exceptions in the related ticket.
