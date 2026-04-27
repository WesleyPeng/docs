# Agentic QA Platform — Public Site

This repository builds and serves [https://wesleypeng.github.io/docs/](https://wesleypeng.github.io/docs/),
the public-facing introduction to the Agentic QA Platform.

## Structure

```
docs/
├── mkdocs.yml                          # MkDocs Material site config
├── requirements.txt                    # Python build dependencies
├── docs/                               # Markdown source for the site
│   ├── index.md                        # Home
│   ├── architecture.md                 # System architecture
│   ├── phases.md                       # Phase-by-phase status
│   ├── test-automation.md              # Agentic-TAF framework
│   ├── tech-stack.md                   # Component versions
│   ├── resources.md                    # Links and external docs
│   ├── about.md                        # Author + repo access
│   └── assessment-report/              # Legacy SW QA Assessment Report
│       ├── index.html                  # (Word-export, preserved as-is)
│       ├── taf.implementation.pdf
│       └── taf.impl.pptx
└── .github/workflows/deploy.yml        # GitHub Actions: build & deploy
```

## Local preview

```bash
pip install -r requirements.txt
mkdocs serve
# Open http://127.0.0.1:8000/docs/
```

## Build

```bash
mkdocs build --strict
# Output goes to ./site/
```

## Deployment

GitHub Actions builds on every push to `master` and deploys to the
`gh-pages` branch. GitHub Pages serves from `gh-pages`.

To deploy manually:

```bash
mkdocs gh-deploy --force --remote-branch gh-pages
```

## License

| Asset | License |
|-------|---------|
| Markdown content | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) |
| `assessment-report/` | Preserved as-is from the prior version of the site |
