<!-- 783012eb-d9cc-41c5-bc86-a78f0f13edd2 29dc07a7-1e09-4fd4-a246-eb05e1949e9e -->
## Repo Flatten + Initial Push to Main, Protect Main, Default Develop

We will flatten the project so `\\wsl.localhost\\Ubuntu-24.04\\home\\kngpnn\\dev\\obsidian-mcp-pro` is the repo root, then initialize Git, make an initial commit, push to `main`, configure protections, and set `develop` as the default branch. Remote repo: `https://github.com/Beaulewis1977/obsidian-mcp-pro` ([GitHub repo](https://github.com/Beaulewis1977/obsidian-mcp-pro)).

### Phase 1 — Flatten safely (pre-push)

1) Pre-flight audit (read-only)
- Search for references to `obsidian-mcp/` in configs, scripts, CI, Docker, docs.
- Note updates needed (build contexts, working directories, script paths).

2) Move contents up one level
- Move all content from `obsidian-mcp/` to the parent; delete the empty folder.

3) Update path references
- `package.json` scripts, workspaces; `tsconfig.json` aliases; import paths.
- Docker/Compose: `context`, `dockerfile`, `workdir`.
- CI (GitHub Actions in `.github/workflows`): `on:` branches (`main`, later `develop`), `working-directory`, caches, `paths` globs.
- Tooling: ESLint/Prettier configs, `.gitignore`, `.env` loader paths, lockfile paths.
- Docs/README links and badges pointing to `obsidian-mcp` → update to `obsidian-mcp-pro`.

4) Review `.github` and CodeRabbit
- Update workflows for `main` (build/test) and later `develop` as default.
- Review `CODEOWNERS`, PR/Issue templates, `dependabot.yml`, `renovate.json`, release tooling.
- Add `.coderabbit.yml` with repo rules (branch filters, labels, path ignores); ensure the CodeRabbit GitHub App is installed and set as a required check on protected branches.

### Phase 2 — Initialize repo and initial push to main

1) Initialize Git at parent
- Remove only `obsidian-mcp/.git` (if present).
- Initialize repo at parent, set default branch to `main` (we push `main` first).
- Add remote `origin` to `https://github.com/Beaulewis1977/obsidian-mcp-pro`.

2) First commit and push
- Stage files; create concise initial commit (e.g., "chore: initialize repo at root").
- Ensure authentication allows pushing workflow files: token must include `workflow` scope when pushing `.github/workflows/*`.
  - If current token lacks scope, either:
    - Refresh `gh` token with: `gh auth refresh --hostname github.com --scopes repo,workflow` (device flow), or
    - Use a PAT (classic) with `repo` and `workflow` scopes for HTTPS push.
- Push `main` to origin.

### Phase 3 — Branch protections and default branch switch

1) Protect `main`
- Require PR reviews (e.g., 1+), dismiss stale reviews, restrict force pushes.
- Require status checks (build/test/linters) to pass before merge.
- Add CodeRabbit as a required check.

2) Create `develop` and set as default
- Create `develop` from `main` and push to origin.
- Set repository default branch to `develop` in GitHub settings.
- Update workflows to trigger on PRs and pushes to `develop` and PRs targeting `main`.

### Phase 4 — Working model going forward

- Feature branches cut from `develop` → PR into `develop`.
- Periodic release PRs from `develop` → `main` (require checks and review).
- Hotfixes can branch from `main` with back-merge to `develop`.

### Validation checklist

- Local build/test works from repo root after flattening.
- Docker builds succeed if applicable.
- CI workflows run green on `main` push.
- Branch protection rules enforced; CodeRabbit check appears in PRs.

### Notes

- GitHub default branch and protection rules require at least one pushed commit to `main`.
- Tune CodeRabbit rules gradually based on PR behavior.
- If preserving the old nested repo history is ever required, use `git subtree` or `git filter-repo` to import history instead of deleting the nested `.git`.

### To-dos

- [ ] Audit codebase for references to `obsidian-mcp/` path
- [ ] Move `obsidian-mcp/*` to parent; remove empty folder
- [ ] Update configs/scripts/CI/Docker paths to new root
- [ ] Init git at parent; set origin; create `main` and `develop` branches
- [ ] Verify dev/build/test work from new root
- [ ] Protect `main`, set `develop` as default, mark CodeRabbit required


