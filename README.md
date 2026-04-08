# LaTeX Template

A LaTeX document template with parallel compilation, themes, math environments, algorithm listings, and syntax highlighting support.

## Prerequisites

- **latexmk** — required for compilation
  ```bash
  # Debian/Ubuntu
  sudo apt install latexmk

  # macOS (Homebrew)
  brew install latexmk

  # Arch Linux
  sudo pacman -S latexmk
  ```
- **Python 3** — optional, only needed for the `./start.sh latex` runner
  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  pip install -r requirements.txt
  ```

## Quick Start

1. Copy the example directory as a starting point:
   ```bash
   cp -r LaTeX/src/example LaTeX/src/my_document
   ```
2. Rename the files inside your new directory:
   ```bash
   cd LaTeX/src/my_document
   mv light_main.tex my_document_main.tex
   mv example_content.tex my_document_content.tex
   ```
3. Edit `my_document_main.tex` and `my_document_content.tex` to suit your needs.
4. Compile:
   ```bash
   ./start.sh latex
   ```
5. Find your PDF at `LaTeX/pdfs/my_document/my_document_main.pdf`.

## Building

```bash
./start.sh latex    # compile all *_main.tex files under LaTeX/src/
```

Output is placed in three mirrored directories:

| Directory | Contents |
|-----------|----------|
| `LaTeX/build/` | latexmk artifacts (`.aux`, `.synctex.gz`, etc.) |
| `LaTeX/pdfs/` | compiled PDFs |
| `LaTeX/logs/` | `.log` files |

To clean all build artifacts:

```bash
rm -rf LaTeX/build/* LaTeX/logs/* LaTeX/pdfs/*
```

## File Structure

```
LaTeX/
├── src/
│   ├── assets/
│   │   ├── algorithms/    # listings and algorithm environments
│   │   ├── environments/  # theorem, definition, and custom environments
│   │   ├── language/      # syntax highlighting (bash, julia, sagemath)
│   │   ├── math/          # amsmath, mathtools, theorem packages
│   │   ├── themes/        # colour themes and theme choice files
│   │   ├── base_header.tex   # core preamble packages
│   │   └── tikz.tex       # pgfplots and tikz configuration
│   └── example/           # light and dark theme examples
├── build/
├── pdfs/
├── logs/
└── run.py
```

## Naming Conventions

These are **required** for the build system to work:

| Pattern | Purpose |
|---------|---------|
| `*_main.tex` | Compilation target — the script finds all of these automatically |
| `*_content.tex` | Document body (tables of contents, sections) |
| `*_content.tex` | Body files are included from the main file via `\input{}` |

Files whose path contains `legacy`, `templates`, `tmp`, or `temp` are **excluded** from compilation.

## Header Load Order

The preamble is split across multiple header files that **must** be loaded in a specific order:

### In your `*_main.tex` file (in this exact order):

```tex
\documentclass[french,a4paper,10pt]{article}
\input{../assets/base_header.tex}
\input{../assets/themes/theme_choice_dark.tex}   % or theme_choice_light.tex
\input{../assets/themes/theme.tex}
```

### In your `*_content.tex` file (in this exact order):

```tex
\input{../assets/math/base.tex}
\input{../assets/math/advanced.tex}
\input{../assets/environments/base.tex}
\input{../assets/tikz.tex}
\input{../assets/algorithms/base.tex}
\input{../assets/algorithms/advanced.tex}
\input{../assets/language/your_languages.tex}   % bash, julia, or sagemath
\input{../assets/themes/more_colours.tex}
```

Loading headers out of order will cause errors because some packages depend on others.
Only the headers in your `*_main.tex` are required for compilation. The ones in `*_content.tex` are optional and can be included as needed.

## Custom Environments

Copy `environments/template.tex` into your own header file and modify it to create custom theorem, definition, or remark environments. Do not load `template.tex` directly.

## Custom Commands

Custom commands are defined in `base_header.tex`. Use `\providecommand` when defining new commands to avoid "command already defined" errors:

```tex
\providecommand{\mycommand}[1]{{\sffamily\bfseries\color{astral}#1}}
```

## Themes

Two themes are provided: `theme_choice_light.tex` and `theme_choice_dark.tex`. The active theme is selected in your `*_main.tex` file.

## Cross-References

Use `\ref{}` or `\eqref{}` for automatic reference type naming:

```tex
\section{Introduction}\label{sec:intro}
Section~\ref{sec:intro} shows that ...   % → "Section 1 shows that ..."
```


## Assets

Place global images and other assets under `src/assets/` and reference them with relative paths.
Place document-specific images in a subdirectory of your document's directory (e.g., `src/my_document/images/`) and reference them with relative paths.