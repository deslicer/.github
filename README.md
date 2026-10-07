# How this repository works

This repository is change control for Splunk configuration. Your team
reviews apps in GitHub. Deslicer applies approved changes to the machine
groups you map in the dashboard. Merging a pull request here does not
apply anything — the automation service does.

## Start here

1. Merge this setup pull request if it is still open.
2. Add an `apps/` folder. One Splunk app per folder.
3. List each app under the right machine group in `.deslicer/environments/`.
4. Open a pull request. Validate runs in GitHub Actions (no plan yet).
5. Merge to `main`/`master` so Plan creates/reconciles at HEAD. Approve, then Deploy, in the Deslicer dashboard → Change requests.

## Validate, Plan, Verify, Approve, Deploy

| Stage | What it does | Where to look |
| --- | --- | --- |
| **Validate** | Diff/validate check on the pull request (no pending plan) | GitHub → Actions (`deslicer-plan.yml` → `validate-diff`) |
| **Plan** | Creates/reconciles a change request after merge to `main`/`master` | GitHub → Actions (`deslicer-plan.yml` → `create-plan`) |
| **Verify** | Previews the change against live servers | `deslicer-verify-plan.yml` (or plan-actions) |
| **Approve** | A person signs off on the change request | Deslicer dashboard → Change requests |
| **Deploy** | Applies the approved change to mapped machine groups | Deslicer dashboard → Change requests |

Reject and status checks live in the same workflow folder if you need them.
Approve and Deploy also exist as GitHub Actions when your team uses GitHub
Environment reviewers. Most teams approve and deploy in the dashboard.

## What this setup adds

```
.github/workflows/deslicer-plan.yml
.github/workflows/deslicer-verify-plan.yml
.github/workflows/deslicer-approve-plan.yml
.github/workflows/deslicer-reject-plan.yml
.github/workflows/deslicer-deploy-plan.yml
.github/workflows/deslicer-plan-status.yml
.github/scripts/
.deslicer/environments/<environment>.yml
.deslicer/environments/README.md
README.md
```

You add `apps/` when you ship the first app. We do not create that folder.

## If you get stuck

**The setup pull request is still open.**
Merge it on GitHub, then return to Deslicer dashboard → GitHub.

**Plan or Verify failed.**
Open the failed run under GitHub → Actions. Confirm repository variable
`DESLICER_API_URL` is set and each plan job uses the correct GitHub
Environment (named after the YAML stem under `.deslicer/environments/`).
Then re-run the workflow.

**No change request appeared.**
Open Deslicer dashboard → Change requests. If the list is empty, open
Deslicer dashboard → GitHub and confirm this repository is connected.

**You are not sure which machines will receive the change.**
Open `.deslicer/environments/` and check the machine groups listed there.
Map branches to machine groups under Deslicer dashboard → GitHub.
