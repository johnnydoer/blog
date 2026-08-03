# Chirpy Blog @ blog.bajirao.dev Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Chirpy Jekyll blog on GitHub Pages at `https://blog.bajirao.dev` (custom domain, project repo `johnnydoer/blog`), with docker-compose local preview.

**Architecture:** Starter template `cotes2020/chirpy-starter` → configure `_config.yml` for custom domain + Vancouver TZ → ship `docker-compose.yml` + `CNAME` → enable Pages with `build_type=workflow` + `cname` *before* pushing (so `actions/configure-pages` emits `base_path=/`) → deploy via starter `pages-deploy.yml`.

**Tech Stack:** Jekyll 4 + `jekyll-theme-chirpy` v7.6, Ruby 3.4 (CI), GitHub Pages Actions, Docker image `mcr.microsoft.com/devcontainers/jekyll:2-bullseye`.

**Current state:** Repo created + cloned to `.`; `main` has template commit; `gh` authed as `johnnydoer`.

---

### Task 1: Create `docker-compose.yml`

**Files:**
- Create: `docker-compose.yml`

- [x] **Step 1: Write file**

```yaml
name: chirpy-blog

services:
  jekyll:
    image: mcr.microsoft.com/devcontainers/jekyll:2-bullseye
    container_name: chirpy-jekyll
    ports:
      - "4000:4000"
    volumes:
      - .:/srv/jekyll
    working_dir: /srv/jekyll
    command: >
      bash -lc "bundle install && bundle exec jekyll serve --host 0.0.0.0 --port 4000 --livereload --force_polling"
    init: true
```

- [ ] **Step 2: Verify** `docker compose config` passes (user runs later: `docker compose up` → http://localhost:4000/)

### Task 2: Create `CNAME`

- [x] **Step 1:** Write file `CNAME` with single line `blog.bajirao.dev`.

### Task 3: Edit `_config.yml`

**Files:**
- Modify: `_config.yml`

- [x] **Step 1:** `timezone: America/Vancouver`
- [x] **Step 2:** `title: Homelab Blog`
- [x] **Step 3:** `tagline: Notes, experiments, and ramblings from my homelab`
- [x] **Step 4:** `description: Personal blog about homelab, self-hosting, and software.`
- [x] **Step 5:** `url: https://blog.bajirao.dev`
- [x] **Step 6:** `github: username: johnnydoer`
- [x] **Step 7:** `twitter: username: ""`
- [x] **Step 8:** `social.name: johnnydoer`, `social.email: ""`, links → `- https://github.com/johnnydoer`
- [x] **Step 9:** `baseurl: ""` already empty

### Task 4: Commit local changes (no push yet)

- [x] **Step 1:** `git add docker-compose.yml CNAME _config.yml docs/`
- [x] **Step 2:** `git commit -m "chore: configure site for blog.bajirao.dev, add compose preview"`

### Task 5: Enable Pages + custom domain (BEFORE push — ordering is critical)

- [x] **Step 1:** `gh api -X POST repos/johnnydoer/blog/pages -f build_type=workflow`
- [ ] **Step 2 (user):** DNS `CNAME blog → johnnydoer.github.io`; verify `dig +short blog.bajirao.dev CNAME`
- [ ] **Step 3:** Once DNS resolves: `gh api -X PATCH repos/johnnydoer/blog/pages -f cname=blog.bajirao.dev`
- [ ] **Step 4:** Verify `gh api repos/johnnydoer/blog/pages` → `cname` set, `status: active` (fallback: Settings → Pages UI)

### Task 6: Push → deploy

- [ ] **Step 1:** `git push origin main`
- [ ] **Step 2:** `gh run watch` → build + htmlproofer + deploy green

### Task 7: Verify

- [ ] **Step 1:** `curl -sI https://blog.bajirao.dev/` → `200 OK`
- [ ] **Step 2:** Homepage renders, no broken `/blog/...` asset paths

### Task 8: Post authoring notes (for user)

- [ ] Posts in `_posts/YYYY-MM-DD-<slug>.md`; front matter: `title`, `date: YYYY-MM-DD HH:MM:SS -0700`, `tags`, `description`. Drafts → `_drafts/`.
