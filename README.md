# Portfolio + Blog (Jekyll, GitHub Pages)

Drop this into your GitHub Pages repo and push. It gives you:
- A clean dark theme
- Blog with posts under `_posts/`
- Projects collection under `_projects/`
- SEO, sitemap, and RSS feed

## Quick Start
1. Ensure your repo is either:
   - `username.github.io` (User site), or
   - Any repo with Pages enabled (Settings ▸ Pages ▸ Source: `main`/`/root`).

2. Copy all files to the repo root and commit:
   ```bash
   git add .
   git commit -m "Initial portfolio+blog site"
   git push
   ```

3. Edit `_config.yml` with your details. Update `url` to your domain if using a custom domain.

4. Write posts:
   - Create files like `_posts/2025-08-13-my-post.md` (YAML front matter required).

5. Add projects:
   - Create Markdown files under `_projects/your-project.md` with front matter:
     ```yaml
     ---
     title: My Project
     summary: One-liner.
     tags: [Tag1, Tag2]
     featured: true
     repo: https://github.com/you/repo
     ---
     Content here…
     ```

## Local Preview (optional)
If you have Ruby installed:
```bash
bundle install
bundle exec jekyll serve
```
Open http://localhost:4000

## Customize
- Colors/spacing in `assets/css/style.css`
- Nav in `_config.yml` under `nav:`
- Add images to `assets/img/`
