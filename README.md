# GithubAction-CICD-StaticWeb  
# gh-deployment-workflow

A minimal project that deploys a static `index.html` to **GitHub Pages** using
**GitHub Actions** — but only when `index.html` actually changes.

## What it does

On every push to the `main` branch that modifies `index.html`, a workflow runs
and publishes the file to GitHub Pages. Pushes that don't touch `index.html`
(e.g. editing this README) do **not** trigger a deploy.

The live site is available at:

```
https://<username>.github.io/gh-deployment-workflow/
```

## Repository layout

```
.
├── index.html                  # the page that gets deployed
├── README.md                   # this file
└── .github/
    └── workflows/
        └── deploy.yml          # the deployment workflow
```

## How the "only on index.html change" rule works

The workflow trigger uses a `paths` filter:

```yaml
on:
  push:
    branches: [ main ]
    paths:
      - "index.html"
```

GitHub compares the files changed in the push against this glob. The job runs
only if `index.html` is among them, so unrelated commits are skipped.

## One-time setup

1. Create a repository named `gh-deployment-workflow` and push these files to `main`.
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment → Source**, select **GitHub Actions**.

That's it. The next push that changes `index.html` deploys automatically.
You can also trigger a deploy manually from the **Actions** tab
(the workflow includes `workflow_dispatch`).

## How the deploy works

The workflow uses GitHub's first-party Pages actions:

- `actions/configure-pages` — prepares the Pages environment.
- `actions/upload-pages-artifact` — packages the site (repo root, `path: "."`).
- `actions/deploy-pages` — publishes the artifact to Pages.

It requests the `pages: write` and `id-token: write` permissions these actions
need, and uses a `concurrency` group so overlapping deploys queue instead of
clashing.

## Testing it

After setup, edit `index.html` (change the heading text), commit, and push to
`main`. Watch the run under the **Actions** tab; when it finishes, refresh the
Pages URL to see the update.
