### 🪴 Git Branching & Commit Guidelines

**Branching:**

- Use clear branch names: `feature/`, `bugfix/`, `hotfix/`, `docs/`, etc.
- Always branch from `main` (or `dev` if using Gitflow).
- Keep branches short-lived and focused on one task.
- Regularly sync with `main` to avoid merge conflicts.
- Delete branches after merging.
- All merges to `main` should go through Pull Requests with code review.

**Committing:**

- Commit often, with small, logical changes.
- Use clear, imperative commit messages:
  `feat(auth): add JWT login`, `fix(ui): correct button color`.
- Reference issue numbers when applicable (e.g. `#123`).
- Don’t commit build files, logs, or secrets — use `.gitignore`.
- Rebase or squash commits before merging to keep history clean.

**Example Workflow:**

```bash
git checkout main
git pull
git checkout -b feature/add-login
# make changes
git add .
git commit -m "feat(auth): add login flow"
git push -u origin feature/add-login
# open PR -> review -> merge -> delete branch
```

---
