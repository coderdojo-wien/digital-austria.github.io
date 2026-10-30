# AGENTS.md

## Overview

Source of the website for "digital.austria – Verein zur Förderung der Informationstechnologie" (<https://digital-austria.at>). Site content is in German (de-at).

## Stack

- Hugo (extended); the version is pinned in `.env` (`HUGO_VERSION`) and used by CI
- Theme [PaperMod](https://github.com/adityatelange/hugo-PaperMod) as a Git submodule in `themes/PaperMod`. **Do not modify it**; override files in `layouts/` instead
- Custom partials in `layouts/partials/`, including the cookie consent banner (`cookie-consent.html`, included through `extend_head.html`)

## Layout

- `content/`: pages and posts (`posts/`, `dojos/`, `infos/`, `page/`, `projects/`)
- `data/events.json`, `data/mentors.json`: data rendered by partials
- `static/`: images, documents, favicons, `CNAME`
- `config.yaml`: site configuration

## Local development

    git submodule update --init      # theme at the committed version; it must match the pinned Hugo
    docker compose up                # http://localhost:1313/

Without Compose:

    . ./.env
    docker run -it --rm -p 1313:1313 -v "$PWD":/project \
      ghcr.io/gohugoio/hugo:v${HUGO_VERSION} server --bind 0.0.0.0 --buildDrafts

The official image is the extended edition and its entrypoint is `hugo`, so pass only the arguments.

`--bind 0.0.0.0` is required inside a container (`-b` is `--baseURL`, not `--bind`); without it the server isn't reachable from the host or Windows. A local Hugo works too: `hugo server --buildDrafts`, using the version from `.env`.

In a `git worktree`, `.git` is a file pointing outside the mount and `enableGitInfo` fails in the container; also mount the main repo's `.git` at its host path.

`public/` and `resources/_gen/` are generated; never commit them.

## Git workflow

- Never push directly to `main`
- Work on feature branches (`feature/...`) or hotfix branches (`hotfix/...`) and open a GitHub pull request into `main`

## Deployment

[.github/workflows/gh-pages.yml](.github/workflows/gh-pages.yml) builds on every push and PR (`hugo --minify`). Only changes merged into `main` deploy, to the `gh-pages` branch (GitHub Pages, custom domain through `static/CNAME`).
