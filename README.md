# gh-mobile-rebase-bug-repro

Minimal reproduction repository for a GitHub mobile app (Android) bug:
selecting **"Update branch (Rebase)"** on a pull request's *Update branch
options* sheet produces a merge commit instead of a rebase.

## Expected behavior

When the **Update branch (Rebase)** option is selected and confirmed, the
PR head branch is rebased on top of the latest `main` and force-pushed.
For a PR with a single commit, the result is a single commit whose parent
is the current `main` tip.

## Observed behavior

A merge commit is added to the PR branch instead, with the canonical
`Merge branch 'main' into <branch>` message and two parents:
`[<original head>, <main tip>]`. This is identical to what the plain
**Update branch** (merge) option does.

## Repro layout in this repo

- `main` has two commits: an initial README commit and a follow-up commit
  that adds `feature-A.md`.
- Branch `feature/example` was branched from the initial commit and has
  one commit that adds `feature-B.md`.
- A pull request from `feature/example` → `main` is open, conflict-free,
  and behind `main` by one commit — exactly the conditions under which a
  rebase should be a clean single-commit replay.

## How to reproduce

1. Open the PR in the GitHub Android mobile app.
2. Tap **Update branch**. Tap the gear / overflow to open *Update branch
   options*.
3. Select **Update branch (Rebase)** and confirm.
4. Observe the resulting commit graph on the PR. The head will be a merge
   commit (two parents) rather than the original commit replayed onto
   `main` (one parent).

For comparison, performing the same update from the web UI produces the
correct rebase result.
