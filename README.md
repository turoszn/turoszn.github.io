# Blog

Jekyll + minima, built natively by GitHub Pages (no workflow file).

## Run locally
    bundle install
    bundle exec jekyll serve
    # → http://localhost:4000

## Go live
1. Create a repo named `<username>.github.io`.
2. Push these files to `main`.
3. Repo → Settings → Pages → Source: **Deploy from a branch** → `main` / root.
4. Live at https://<username>.github.io within a minute or two.

## Design notes
- Measure/fonts/colors: `assets/main.scss` (top block).
- Table of contents: `* toc` + `{:toc}` at the top of a post.
- Footnotes: `text[^id]` … `[^id]: note`.
- Heading anchors + analytics stub: `_includes/custom-head.html`.
