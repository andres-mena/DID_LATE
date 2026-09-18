# Paper

This directory contains the current LaTeX source for the DID_LATE working paper.

Main files:

- `main.tex`: paper entry point.
- `sections/`: modular section files.
- `biblio.bib`: bibliography.
- `aea.bst`: bibliography style used by `main.tex`.
- `DID_LATE_latest.pdf`: latest copied PDF snapshot from the source project.

The paper expects frozen exhibits in the repository-level `output/` directory.
From this directory, the relative paths look like `../output/tables/...` and
`../output/figures/...`.

Compile from this directory:

```bash
latexmk -pdf main.tex
```

The source files are newer than the copied PDF snapshot in a few sections, so
if LaTeX is available the source should be treated as canonical.
