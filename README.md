# jessefmoore.github.io

Personal security blog — pure static HTML. No Jekyll, no build step, no dependencies.

## Design

Terminal aesthetic inspired by the JFM hacker persona:
- Dark background (`#0d1117`), IBM Plex Mono, phosphor-green headings
- Two-column layout: left sidebar (nav + on-page TOC) + main content
- ASCII art JFM logo in header
- `.post-card` grid on home page, `.report-card` grid for pentest reports

Shared stylesheet: `assets/style.css`

## Structure

```
.
├── index.html              # Home page — posts list + reports grid
├── posts/
│   ├── index.html          # All posts listing
│   ├── 2026-05-23-claude-code-cli.html
│   ├── 2020-05-16-htb-heist.html
│   ├── 2020-02-07-htb-bastion.html
│   ├── 2019-12-01-shodan-cli.html
│   ├── 2019-02-05-cisa-ta18-074a.html
│   ├── 2018-11-21-kansa-powershell.html
│   ├── 2018-06-01-webapp-pentest.html
│   └── 2018-03-01-pentest-project.html
├── reports/
│   ├── index.html          # Reports listing
│   └── lehack2024/
│       ├── report.html     # Standard pentest report (LeHack 2024)
│       └── casebook.html   # CRT-aesthetic operator casebook (LeHack 2024)
└── assets/
    └── style.css           # Global stylesheet
```

## Deployment

GitHub Actions deploys to GitHub Pages on push to `main`:

```yaml
# .github/workflows/deploy.yml
- uses: actions/checkout@v4
- uses: actions/configure-pages@v5
- uses: actions/upload-pages-artifact@v3
  with:
    path: '.'
- uses: actions/deploy-pages@v4
```

No build step. The entire repo is uploaded as the artifact and served as-is. `.nojekyll` disables Jekyll processing.

## Adding a Post

1. Create `posts/YYYY-MM-DD-slug.html` — copy the header/sidebar structure from any existing post
2. Add an entry to `posts/index.html` and to the post list in `index.html`
3. Update the sidebar "recent posts" in `index.html` if it's one of the 4 most recent

## Adding a Report

1. Create `reports/<slug>/report.html` (and optionally `casebook.html`)
2. Add an entry to `reports/index.html` and to the reports grid in `index.html`
