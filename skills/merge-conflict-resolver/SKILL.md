---
name: merge-conflict-resolver
description: Resolve git merge, rebase, and cherry-pick conflicts interactively. Use this skill whenever the user mentions merge conflicts, conflict markers, failed merges, rebase conflicts, or asks to resolve conflicts in their repository. Also trigger when the user says things like "fix my merge", "I have conflicts", "help me rebase", or "resolve the conflicts". Even if they don't say "merge conflict" explicitly, if context suggests they're dealing with conflicting changes in git, use this skill.
---

# Merge Conflict Resolver

Resolve git merge conflicts by analyzing both sides, recommending a resolution, and letting the user make the final call.

## When this skill activates

The user has one or more files with unresolved git conflicts — whether from `git merge`, `git rebase`, `git cherry-pick`, or `git stash pop`. They want help understanding what happened and choosing the right resolution.

## Step 1: Assess the situation

Run these commands to build context:

```bash
git status
```

This tells you which files are in conflict (look for "both modified", "both added", "deleted by us/them", etc.) and what operation is in progress (merge, rebase, cherry-pick).

If it's a merge:
```bash
git log --oneline --graph -10
```

If it's a rebase or cherry-pick, also check:
```bash
git rebase --show-current-patch 2>/dev/null || true
```

Summarize the situation to the user briefly: what operation caused the conflicts, how many files are affected, and which branches/commits are involved.

## Step 2: Analyze each conflicted file

For each conflicted file, read it and locate the conflict markers:

- `<<<<<<< HEAD` (or `<<<<<<< ours`) — the current branch's version
- `=======` — separator
- `>>>>>>> branch-name` (or `>>>>>>> theirs`) — the incoming branch's version

For each conflict hunk, understand what both sides are doing. Use git history for additional context when the intent isn't obvious:

```bash
git log --oneline -5 -- <file>
git log --oneline -5 MERGE_HEAD -- <file> 2>/dev/null || true
```

This helps you understand *why* each side made its changes, which is critical for a good recommendation.

## Step 3: Present conflicts and recommend

For each conflict hunk, present the user with:

1. **File and location** — which file, which lines
2. **"Ours" (current branch)** — what this side does and likely why
3. **"Theirs" (incoming branch)** — what this side does and likely why
4. **Recommendation** — your suggested resolution and reasoning

Your recommendation should be one of:
- **Keep ours** — when the current branch's version is correct or more complete
- **Keep theirs** — when the incoming branch's version is correct or more complete
- **Combine both** — when both sides made independent, non-overlapping changes that should coexist (show the proposed combined version)
- **Rewrite** — when neither side is fully correct and a new version is needed (show the proposed rewrite)

Always show the exact code you're proposing as the resolution so the user can evaluate it.

Present conflicts one file at a time. If there are many files, group trivial conflicts (e.g., import ordering, whitespace) separately from substantive ones.

## Step 4: Apply the resolution

Only after the user approves (or modifies) your recommendation for each conflict:

1. Edit the file to replace the conflict markers with the agreed-upon resolution
2. Verify no conflict markers remain in the file:
   ```bash
   grep -n "^<<<<<<< \|^=======$\|^>>>>>>> " <file>
   ```
3. Stage the resolved file:
   ```bash
   git add <file>
   ```

Repeat for each conflicted file.

## Step 5: Wrap up

After all files are resolved and staged, tell the user what to do next based on the operation:

- **Merge**: "All conflicts resolved and staged. You can complete the merge with `git commit` (git will use the default merge commit message) or I can do it for you."
- **Rebase**: "All conflicts in this step are resolved. Run `git rebase --continue` to proceed, or I can do it for you." (Note: there may be more conflicts in subsequent rebase steps.)
- **Cherry-pick**: "All conflicts resolved. Run `git cherry-pick --continue` to finish, or I can do it for you."

Do not automatically commit or continue — let the user decide.

## Edge cases

- **Binary files**: Flag these to the user — they can't be merged textually. Ask which version to keep, or if they need to manually re-create the file.
- **Deleted vs modified**: When one side deleted a file and the other modified it, explain the situation clearly and ask whether to keep the file (with modifications) or delete it.
- **Massive conflicts**: If there are more than 10 conflicted files, offer to triage — resolve the simple ones first, then focus on the complex ones.
- **Conflict markers in strings/comments**: Be careful not to confuse actual conflict markers with strings that happen to contain `<<<<<<<`. Real conflict markers start at column 0 and follow the exact pattern.
