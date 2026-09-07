# Preserve the pre-worker tree

Snapshot before every fire or refire, including a clean tree. Put `$JOB` outside
the repository, or under a directory Git already ignores. Run from the repository
root. Use a private index so the owner's staging area stays untouched.

```bash
git rev-parse HEAD > "$JOB/baseline.head"
git diff --cached --binary > "$JOB/baseline.staged.patch"
git status --short --untracked-files=all > "$JOB/baseline.status"
(
  set -e
  snapshot_index="$JOB/snapshot.index"
  trap 'rm -f "$snapshot_index"' EXIT
  export GIT_INDEX_FILE="$snapshot_index"
  git read-tree HEAD
  git add -A
  git write-tree > "$JOB/baseline.tree"
  git diff --cached --binary HEAD > "$JOB/pre-fire.patch"
)
```

- `baseline.tree` includes working-tree bytes for tracked and untracked,
  nonignored files, deletions, modes and symlinks. `baseline.staged.patch`
  separately preserves staged content that differs from working-tree bytes.
- `git write-tree` creates local Git objects; it does not commit, switch branches,
  or modify the real index. Keep the job until review/fixes finish. Do not run Git
  pruning during an active job; these trees are not commit-reachable.
- A submodule tree records its gitlink, not its uncommitted contents. If a task
  touches a dirty submodule or mounted repository, take a separate snapshot in
  that repository and record its location. Never claim the host snapshot covers it.
- At review, capture the post-worker tree in a **new** job directory with the same
  recipe. Compare `git diff <baseline-tree> <post-tree>` and the corresponding
  `--name-status` output. This includes worker-created untracked files; comparing
  only ordinary `git diff` or status cannot establish their original content.
- `pre-fire.patch` remains a readable HEAD-to-baseline patch for context. Use the
  two tree ids for worker attribution; staged and unstaged patches are not the
  worker's delta. Concurrent changes still require line-by-line attribution.
- Never restore a baseline over the owner's work without an explicit restoration
  instruction. The snapshot is evidence, not permission to reset the tree.
