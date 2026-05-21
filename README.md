# nvidia-repos

`nvidia-repos` is a small static catalog of active NVIDIA GitHub repositories.
It groups public NVIDIA repositories into rough technology clusters and keeps
GitHub metadata fresh with a scheduled update script.

Included repositories match this filter:

- GitHub org: `NVIDIA`
- At least 200 stars
- Non-fork and non-archived
- Last pushed within the previous three calendar months

The page also includes adjacent active Jetson/edge-AI repository sections from
`NVIDIA-AI-IOT`, `jetsonhacks`, and `dusty-nv`; those sections require only a
commit within the previous three calendar months.

## Open locally

Open `index.html` directly in a browser, or run a local preview server:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

## Update repository metadata

```bash
python3 update_stats.py
```

The script fetches stars, forks, total commits, recent commits, last commit
dates, primary language, topics, and descriptions from GitHub. It also rewrites
the first "most traction" table, the cluster tables below it, and the adjacent
Jetson/edge-AI source sections.

For local runs, authenticate with the GitHub CLI or set `GITHUB_TOKEN`:

```bash
gh auth login
python3 update_stats.py
```

## Main files

- `index.html` - generated static catalog page.
- `update_stats.py` - GitHub metadata fetcher and HTML generator.
- `stats_history.json` - stored snapshots used for 30-day star/fork deltas.
- `favicon.svg` - NVIDIA-themed favicon.
- `.github/workflows/static.yml` - deploys the static site to GitHub Pages.
- `.github/workflows/update-stats.yml` - scheduled catalog refresh.
