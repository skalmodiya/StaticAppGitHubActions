# Deployment Progress — Static App to SAP BTP Cloud Foundry

**Repo:** https://github.com/skalmodiya/StaticAppGitHubActions
**App name on CF:** `static-btp-app`
**Live URL:** https://static-btp-app.cfapps.us10-001.hana.ondemand.com

---

## Checklist

### Phase 1 — Local Setup
- [x] `index.html` — static app (click counter demo)
- [x] `manifest.yml` — CF deploy config (staticfile_buildpack, 64M)
- [x] `Staticfile` — tells CF nginx to serve from root
- [x] `.cfignore` — excludes .git, .github, logs etc from cf push
- [x] `.github/workflows/deploy.yml` — GitHub Actions CI/CD pipeline
- [x] `.gitignore`

### Phase 2 — GitHub
- [x] Git initialized and first commit made
- [x] Repo created: https://github.com/skalmodiya/StaticAppGitHubActions
- [x] Code pushed to `main` branch

### Phase 3 — GitHub Secrets
- [x] `CF_CONFIG` — full CF CLI config JSON (from `cat ~/.cf/config.json`)
- [x] `CF_TOKEN`  — fresh oauth token (from `cf oauth-token`)
- [x] `CF_ORG`    — BTP cockpit → Cloud Foundry → Org name
- [x] `CF_SPACE`  — your CF space, e.g. `dev`

### Phase 4 — First Deployment
- [x] Pipeline triggered via push to `main`
- [x] Watched job at: https://github.com/skalmodiya/StaticAppGitHubActions/actions
- [x] App is LIVE at: https://static-btp-app.cfapps.us10-001.hana.ondemand.com

### Phase 5 — CI/CD Verified  ← YOU ARE HERE
- [ ] Make a small change to `index.html`, commit and push to `main`
- [ ] Pipeline runs automatically and deploys the new version
- [ ] Confirm updated app is live at the CF URL

---

## IMPORTANT: CF_TOKEN expires every ~10-12 hours

Because your account uses SSO, the bearer token is short-lived.
Before each deploy (or when the pipeline fails with auth errors), refresh it:

```bash
cf login --sso          # login via browser
cf oauth-token          # copy the full "bearer eyJ..." output
```

Then update the `CF_TOKEN` secret at:
https://github.com/skalmodiya/StaticAppGitHubActions/settings/secrets/actions

---

## How CI/CD works (reference)

```
Edit code locally
      │
      ▼
git add . && git commit -m "..." && git push
      │
      ▼  push to main triggers workflow
GitHub Actions:
  1. Checkout code
  2. Install CF CLI v8
  3. Restore CF_CONFIG + patch AccessToken with CF_TOKEN
  4. cf push --strategy rolling   (zero downtime deploy)
  5. New version is live on BTP
```

---

## Key file reference

| File | Purpose |
|---|---|
| `index.html` | The entire app |
| `manifest.yml` | CF config — app name, memory, buildpack |
| `Staticfile` | Tells CF nginx: serve files from root |
| `.cfignore` | Excludes dev files from `cf push` upload |
| `.github/workflows/deploy.yml` | CI/CD — deploys on every push to `main` |

---

## GitHub Secrets reference

| Secret | How to get it |
|---|---|
| `CF_CONFIG` | Run `cat ~/.cf/config.json` locally — copy full JSON. One-time setup. |
| `CF_TOKEN` | Run `cf oauth-token` locally — copy full `bearer eyJ...`. Refresh every ~10h. |
| `CF_ORG` | BTP Cockpit → Cloud Foundry → Org Name |
| `CF_SPACE` | The space inside your org, e.g. `dev` |

---

*Last updated: Phase 4 COMPLETE — app deployed. Phase 5: verify CI/CD with a code change.*
