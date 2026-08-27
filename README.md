# ProteoformTracker Docs

Source for the [ProteoformTracker](https://github.com/HuangLabAtUAB/ProteoformTracker) user
documentation, built with [MkDocs](https://www.mkdocs.org/) +
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/).

## Building locally

```sh
pip install -r requirements.txt
mkdocs serve
```

Then open <http://127.0.0.1:8000>.

## Publishing

This repo is set up two ways -- use whichever you prefer, or both:

- **GitHub Pages**: `.github/workflows/gh-pages.yml` builds and deploys to the `gh-pages`
  branch on every push to `main`. Enable it once under the repo's *Settings → Pages → Source →
  Deploy from a branch → `gh-pages`*.
- **Read the Docs**: `.readthedocs.yaml` is ready to go -- import this repo at
  [readthedocs.org](https://readthedocs.org/) and it will build automatically on every push.

## Screenshots

All screenshots live in `docs/assets/screenshots/` and were captured from a live run of the app
(gene `BCL2L1`, top-down mode) unless the page says otherwise. Re-capture and replace them after any
UI change that makes a screenshot stale.
