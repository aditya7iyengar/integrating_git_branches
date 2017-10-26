# INTEGRATING GIT BRANCHES

This is a Patat Presentation that talks about merge vs rebase and how we can
use them to integrate git branches.

It covers:

- Basics of Merging and Rebasing
- Differences between them
- Things to keep in mind while rebasing
- When to use `git merge` vs `git rebase`
- Interative rebasing


# MERGE vs REBASE

| Merge                                      | Rebase                                |
|:-------------------------------------------|:--------------------------------------|
| Preserves Git History                      | Doesn't preserve git history          |
| Has extra commits for merging              | Doesn't have extra commits            |
| Increases connectivity of Git Graph by one | Keeps the Graph linear                |
| Non-Destructive Operation                  | Destructive Operation                 |

