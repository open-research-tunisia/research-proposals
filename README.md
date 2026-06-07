# Research Proposals

Source for a small GitHub Pages site listing short research proposals that are open for collaboration.

- **Live site:** https://open-research-tunisia.github.io/research-proposals/
- **Homepage source:** [`index.md`](index.md)
- **Proposals:** one folder each (e.g. [`phase-conditioned-sparsity/`](phase-conditioned-sparsity/)), each with an `index.md` and a `figures/` directory.

## Add a new proposal

1. Create a folder, e.g. `my-new-idea/`.
2. Add `index.md` starting with YAML front matter:
   ```yaml
   ---
   title: "My New Idea"
   description: "A research proposal — seeking collaborators"
   ---
   ```
   followed by the proposal body (Markdown). Put any images in `my-new-idea/figures/`.
3. Add a bullet linking to it in the top-level [`index.md`](index.md).

## Build locally (optional)

```bash
gem install bundler jekyll
jekyll serve   # then open http://localhost:4000/research-proposals/
```
