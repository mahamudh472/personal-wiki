# Git Tips & Tricks
Date: 27 Jun 2025 <br>
Tags: Git

✅Top 30 Git Commands Every Developer Must Know !
 
## Daily Git Basics
1. git init – Start a new repo
2. git clone – Copy a remote repo
3. git status – Check repo state
4. git add – Stage changes
5. git commit – Save changes
6. git log – View commit history
 
## Branching (Real Dev Life)
7. git branch – List branches
8. git branch <name> – Create branch
9. git checkout – Switch branch
10. git checkout -b – Create + switch
11. git merge – Merge branches
12. git rebase – Clean commit history
 
## Remote & Collaboration
13. git remote -v – Check remotes
14. git pull – Fetch + merge changes
15. git push – Upload commits
16. git fetch – Download changes
17. git pull --rebase – Clean sync
18. git push -u origin main – Set upstream
 
## Undo Like a Pro
19. git restore – Discard changes
20. git reset – Unstage commits
21. git revert – Safe undo commit
22. git stash – Save work temporarily
23. git stash pop – Restore stashed work
24. git reset --hard – Full rollback
 
## Debugging & Inspection
25. git blame – Who broke this?
26. git show – Inspect commit
27. git reflog – Recover lost commits
28. git diff – See code changes
 
## Power Commands
29. git cherry-pick – Pick specific commit
30. git clean -fd – Remove junk files


## Commit message idea

| Type       | Purpose                                    |
| ---------- | ------------------------------------------ |
| `feat`     | New feature                                |
| `fix`      | Bug fix                                    |
| `refactor` | Code restructuring without behavior change |
| `test`     | Add or modify tests                        |
| `docs`     | Documentation changes                      |
| `chore`    | Maintenance tasks, dependencies, configs   |
| `style`    | Formatting, linting, whitespace            |
| `perf`     | Performance improvements                   |
| `ci`       | CI/CD changes                              |
| `build`    | Build system or dependency changes         |

Example:

```
feat(auth): add Google login
fix(redis): reconnect on startup failure
refactor(users): move validation into service layer
test(payments): add webhook tests
ci(github): run coverage checks
```

> Git bare repo will be added soon...
