# LAB-1113 · Quarkus, containers, and IBM Bob

**IBM TechXchange 2026 · 90-minute hands-on lab**

Build a Quarkus REST service with IBM Bob and the Quarkus Agent MCP, persist greetings in PostgreSQL, and run the application in a Podman container. An optional exercise adds a Qute UI.

**[Open the lab guide](https://myfear.github.io/techxchange-2026-quarkus-bob-lab-1113/)**

- [Download the PDF guide](docs/downloads/LAB-1113-Lab-Guide.pdf)
- [Download the introduction slides](docs/downloads/LAB-1113-Lab-Intro.pptx)
- [Read the publishing instructions](PUBLISHING.md)

## Attend the lab

Start with the [lab overview](docs/lab-overview.md), then [check your environment](docs/exercises/environment.md). Use **Quarkus 3.39.5** for the application, even if the installed Quarkus CLI reports a different version.

Create an empty workspace in IBM Bob. You build `greeting-service` during the exercises; this repository contains the guide and its supporting materials.

## Preview the site locally

Use Python 3.12 or later:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-docs.txt
python -m mkdocs serve
```

Open the URL printed by MkDocs. The preview uses the same `/techxchange-2026-quarkus-bob-lab-1113/` path as GitHub Pages.

Before publishing, check the production build:

```bash
python -m mkdocs build --strict
```

## Repository layout

| Path | Contents |
| --- | --- |
| `docs/index.md` | Lab landing page and schedule |
| `docs/lab-overview.md` | Outcomes, workflow, and terminology |
| `docs/exercises/` | Sequential exercises and optional Qute stretch |
| `docs/troubleshooting.md` | Recovery prompts and environment fixes |
| `docs/assets/images/` | Ten screenshots from the verified lab run |
| `docs/downloads/` | PDF guide, introduction slides, and workspace templates |
| `mkdocs.yml` | Navigation, Material theme, search, and link validation |
| `.github/workflows/docs.yml` | Pull-request validation and Pages deployment |
| `PUBLISHING.md` | Setup, updates, downloads, and recovery |

GitHub Actions validates pull requests and publishes successful `main` builds using Pages artifacts. Deployment does not create commits or push a `gh-pages` branch.
