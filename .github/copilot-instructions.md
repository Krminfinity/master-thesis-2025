# Copilot instructions for this repo

This repository is a LuaLaTeX-based master thesis project (Japanese document). Optimize all edits and automations for LuaLaTeX, VS Code tasks, and the existing directory/output conventions.

## Project map and roles
- Main entry: `修論.tex` (document root). Keep it as the build target.
- References: `my_references.bib` (natbib + bibtex flow; not biblatex/biber).
- Build config: `.latexmkrc` sets LuaLaTeX and `out/` as the output directory.
- VS Code: `.vscode/tasks.json` provides build/clean; `.vscode/settings.json` configures LaTeX Workshop (recipes, outDir, PDF viewer).
- Git ignore: `.gitignore` excludes intermediates and the entire `out/` directory, but keeps `out/*.pdf`.

## Build workflows (Windows PowerShell)
Preferred (inside VS Code):
- Task `Build LaTeX (LuaLaTeX)` → runs `latexmk -lualatex -synctex=1 -interaction=nonstopmode -file-line-error -output-directory=out 修論.tex`.
- Task `Clean LaTeX` → removes intermediates under `out/`.

Alternatively (terminal):
- `latexmk -lualatex -synctex=1 -interaction=nonstopmode -file-line-error -output-directory=out "修論.tex"`

LaTeX Workshop integration (from `.vscode/settings.json`):
- Default recipe: `lualatex`; full chain available: `lualatex -> bibtex -> lualatex -> lualatex`.
- Auto build on save: `latex-workshop.latex.autoBuild.run = onSave`.
- Output dir: `./out`, PDF viewer in editor tab.

## Authoring conventions (follow existing patterns)
- Engine: LuaLaTeX with `luatexja` for Japanese. Do not switch to XeLaTeX/pLaTeX.
- Citations: `natbib` is loaded; use `\citet{...}` / `\citep{...}`. When bibliography changes, use the full recipe (bibtex in the middle) or run build twice after bibtex.
- Hyperlinks: `\usepackage[hidelinks]{hyperref}` is present—keep link styling consistent (no colored boxes).
- Figures/TikZ: `graphicx` and `tikz` with `positioning, arrows.meta` are used; keep these libraries and prefer vector graphics where possible.
- Theorems and names: custom environments exist (e.g., `defn`=「定義」, `theorem`=「定理」, `remark`, `claim`, `proposition`) and `\proofname` is redefined to 「証明」—reuse these for consistency.
- Spacing/layout: `setspace`, `geometry`, `caption` are configured—avoid ad-hoc overrides that conflict with them.

## Repo-specific do/don’t
- Do target `修論.tex` as the root when adding `\input{...}`/`\include{...}` for splits.
- Do keep outputs in `out/`; only PDFs in `out/` are committed (`!out/*.pdf`). Don’t commit other intermediates (`*.aux`, `*.log`, etc.).
- Don’t introduce `biblatex`/`biber`; the toolchain is `natbib` + `bibtex` and already wired.
- Don’t change the engine or output directory—`latexmk` and tasks expect `lualatex` and `out/`.

## Typical tasks for agents (examples)
- Build now: run VS Code task `Build LaTeX (LuaLaTeX)` or the terminal command above.
- Rebuild after citation updates: run the recipe `lualatex -> bibtex -> lualatex*2` (LaTeX Workshop) or call `latexmk -lualatex` twice with a `bibtex` in between.
- Clean: run task `Clean LaTeX` (maps to `latexmk -C -output-directory=out`).

## Notes and pitfalls
- Non-ASCII filename: the entry file is `修論.tex`. When scripting, quote the filename.
- Package order is intentional (e.g., `hyperref` with `hidelinks` after core packages). Avoid reordering unless fixing a concrete conflict.
- Image paths must be relative to the project; the `out/` directory is for outputs only.

If anything here looks inaccurate or you need additional conventions documented (e.g., section file split strategy), ask to refine this file.
