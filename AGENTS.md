# AGENTS.md

This file is the working guide for agents editing `nvidia-repos`.

## What this repo is

`nvidia-repos` is a single-page static catalog of public NVIDIA GitHub
repositories. It follows the same lightweight shape as the reference
`claw-eco` project: no framework, no package manager, and no build step.

The page is generated from GitHub metadata by `update_stats.py`.

## First rule: preserve generated ownership

Most changes to repository rows, cluster contents, timestamps, and stats should
be made in `update_stats.py`, then regenerated with:

```bash
python3 update_stats.py
```

Avoid hand-editing generated table rows in `index.html`; those edits will be
overwritten by the next scheduled refresh.

## Repo layout

- `index.html`: generated static page with embedded CSS and JavaScript.
- `update_stats.py`: fetches GitHub metadata, assigns clusters, ranks tables,
  rewrites `index.html`, and updates `stats_history.json`.
- `stats_history.json`: lightweight history used for 30-day star and fork
  deltas. Keep it committed so scheduled runs can compute traction.
- `favicon.svg`: static favicon.
- `.github/workflows/static.yml`: deploys to GitHub Pages.
- `.github/workflows/update-stats.yml`: scheduled refresh and deploy.

## Catalog rules

Included repositories must be:

- Owned by the `NVIDIA` GitHub org.
- Non-forks and non-archived.
- At least 200 stars.
- Pushed within the last three calendar months.

Additional adjacent source sections are generated from:

- `NVIDIA-AI-IOT`
- `jetsonhacks`
- `dusty-nv`

Those adjacent sections require at least one commit within the last three
calendar months. They do not use the 200-star threshold.

The page contains six rough technology clusters:

1. AI, LLM & Model Tooling
2. GPU Computing, CUDA & Systems
3. Robotics, Simulation & Edge AI
4. Data Science, RAPIDS & Analytics
5. Cloud Native, Infrastructure & DevOps
6. Graphics, Vision & Media

Each cluster table shows up to the top 25 repositories by stars.

The first table is the traction table. It shows the top 25 repositories ranked
by recent commit activity plus stored star and fork deltas when a 30-day history
baseline exists. On first run, it uses a baseline activity score until scheduled
snapshots mature.

## Rebuilding the catalog

Run a full rebuild whenever you want to refresh the qualifying repository set
and recluster the catalog, for example once a month:

```bash
python3 update_stats.py
```

That command queries GitHub for current NVIDIA repositories matching the catalog
rules, fetches metadata and recent commit counts, assigns each repo to a rough
technology cluster, rebuilds the first traction table, rebuilds the six cluster
tables, fetches the adjacent Jetson/edge-AI source sections, rewrites
`index.html`, and updates `stats_history.json`.

Commit both generated files after a rebuild:

```bash
git add index.html stats_history.json
```

If you change cluster definitions, scoring, columns, or table order, edit
`update_stats.py` first and regenerate rather than editing `index.html` by hand.

## GitHub Actions

The scheduled workflow writes directly to the default branch when metadata
changes. Before starting manual work in an existing checkout, inspect status and
pull safely:

```bash
git status --short --branch
git fetch origin
git pull --ff-only origin main
```

If the worktree is dirty, do not discard local changes you did not make.
