# Publish the LAB-1113 guide

The public site is **[LAB-1113 on GitHub Pages](https://myfear.github.io/techxchange-2026-quarkus-bob-lab-1113/)**. MkDocs builds the Markdown, screenshots, and downloads under `docs/`. GitHub Actions publishes a successful build from `main`.

## Repository setup

In [Settings → Pages](https://github.com/myfear/techxchange-2026-quarkus-bob-lab-1113/settings/pages), set **Build and deployment → Source** to **GitHub Actions**.

The `github-pages` environment must permit deployments from `main`. If environment reviewers are configured, approve the pending deployment in Actions.

The workflow uses GitHub's built-in token. It requires no personal token or repository secret. The build has read-only source access; the deployment job has `pages: write` and `id-token: write`. See [GitHub's custom Pages workflow documentation](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages).

## Publish a content change

1. Edit the relevant Markdown under `docs/`. Preserve code and prompts when making layout changes.
2. Update downloads if the approved PDF guide, slides, or workspace templates changed.
3. In the repository's virtual environment, validate the production build:

    ```bash
    python -m pip install -r requirements-docs.txt
    python -m mkdocs build --strict
    ```

4. Preview with `python -m mkdocs serve`. Check the changed page, navigation, copied code, and any downloads.
5. Commit and push with your normal Git identity. Pull requests run **Build documentation** without deploying. Merging or pushing to `main` builds and publishes the site.
6. In [Actions](https://github.com/myfear/techxchange-2026-quarkus-bob-lab-1113/actions/workflows/docs.yml), confirm that both **Build documentation** and **Publish GitHub Pages** succeed. Open the deployment URL and verify the changed page.

The strict build fails on warnings, including missing local links, anchors, images, and navigation entries. It does not check whether external websites are reachable or rerun the Quarkus lab.

To republish without a source change, open **Actions → Build and publish lab guide → Run workflow** and select **main**. Manual runs on other branches only validate the build.

## Publish downloadable documents

The files in `docs/downloads/` are copied unchanged into the Pages artifact. Replacing a file and publishing `main` updates its existing download URL.

- `LAB-1113-Lab-Guide.pdf`: the final 28-page guide, exported from Word with the lab-run screenshots.
- `LAB-1113-Lab-Intro.pptx`: the introduction deck.
- `AGENTS.md.txt`: workspace rules, offered with the download filename `AGENTS.md`. The `.txt` suffix keeps MkDocs from rendering it as a site page.
- `mcp-quarkus-agent.json`: MCP template, offered with the download filename `mcp.json`.

Keep the editable Word source outside this repository. Its instructional text, tables, and all 29 code/prompt blocks were checked against the Markdown before splitting the web exercises. The web pages and downloadable guide are separate maintained artifacts; this workflow does not regenerate the PDF or Office documents.

When changing the guide:

1. Update your local Word source and the corresponding web exercises.
2. In Microsoft Word, refresh the table of contents and export as PDF using **Save As → PDF → Best for printing**. Word preserves the guide's pagination; LibreOffice can paginate it differently.
3. Set the PDF title to the lab title and the authors to Markus Eisele and Alex Soto. Check that troubleshooting links point to `https://myfear.github.io/techxchange-2026-quarkus-bob-lab-1113/troubleshooting/` with their section anchors. Replace any original IBM-internal URLs when exporting the public guide.
4. Inspect every exported page, including the contents-page numbers, code blocks, and screenshots, then replace `docs/downloads/LAB-1113-Lab-Guide.pdf`.
5. Build and publish as described above.

## Update images

The ten images in `docs/assets/images/` are reused from the verified lab run. Keep filenames when replacing a screenshot to preserve links. Add meaningful alt text and retain the textual steps so an image is never the only instruction.

To add a new image, place it in that directory, link it relative to the Markdown page, and run the strict build. The current site needs no cover artwork to publish.

## Add the short URL later

1. Point the short URL at `https://myfear.github.io/techxchange-2026-quarkus-bob-lab-1113/`.
2. Add it at the marked comment in `docs/index.md`, for example as a **Quick access** callout.
3. Update the introduction slides and Word source if they need the short URL or a QR code, then export and verify a new PDF guide.
4. Publish and test the redirect.

Keep `site_url` set to the full GitHub Pages URL. A short URL is a redirect, not a custom domain; it requires no `CNAME` file or DNS change for this repository.

## Build or deployment failures

- **Build fails:** inspect the failed Actions step, reproduce with `python -m mkdocs build --strict`, and fix the reported page, link, or dependency.
- **Pages is not configured:** select **GitHub Actions** in Settings → Pages and rerun the workflow on `main`.
- **Deployment awaits approval:** review the `github-pages` environment's pending deployment.
- **Deployment is rejected:** confirm that the environment allows `main` and repository policy permits the official Pages actions.
- **Need to restore an earlier version:** revert the content change through your normal Git workflow and publish `main` again.

Never commit `site/` or `.venv/`. Deployment uploads the built site as an artifact; it does not run `mkdocs gh-deploy`, create bot-authored commits, or push a `gh-pages` branch.

## Update dependencies

`requirements-docs.txt` pins MkDocs, Material for MkDocs, and PyMdown Extensions. Update those versions deliberately, run a strict build, and check both light and dark themes plus a narrow browser viewport. Workflow action versions are pinned to verified major release tags.
