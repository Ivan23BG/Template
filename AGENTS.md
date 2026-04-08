# AGENTS.md

This is a LaTeX document template project. Compilation is managed by `LaTeX/run.py` and invoked via `./start.sh latex`.

## Project Structure

```
/home/ivan/Programming/Projects/Template/
├── LaTeX/
│   ├── src/
│   │   ├── assets/
│   │   │   ├── algorithms/   # listings / algorithm environments
│   │   │   ├── environments/ # custom theorem/definition environments
│   │   │   ├── language/     # syntax highlighting for bash, julia, sagemath
│   │   │   ├── math/        # amsmath, mathtools, theorem packages
│   │   │   ├── themes/      # colour themes and theme choice files
│   │   │   ├── base_header.tex  # core packages (fontenc, babel, geometry, hyperref, etc.)
│   │   │   └── tikz.tex     # pgfplots and tikz configuration
│   │   ├── example/         # light/dark theme examples
│   │   └── ...              # your documents go here
│   ├── build/               # latexmk artifacts (compiled PDFs, aux, synctex, etc.)
│   ├── pdfs/               # output PDFs (mirrors src/ structure)
│   ├── logs/               # .log files (mirrors src/ structure)
│   └── run.py              # compilation runner — use via ./start.sh latex
├── .venv/                  # Python virtual environment
├── requirements.txt
└── start.sh                # entry point script
```

---

## Build Commands

### Compile LaTeX

```bash
./start.sh latex
```

This runs `LaTeX/run.py` which:
- Recursively finds all `*_main.tex` files under `LaTeX/src/`
- Compiles each in parallel using `latexmk`
- Mirrors their output into `build/`, `pdfs/`, and `logs/`

### Clean

```bash
./start.sh clean    # removes __pycache__ and .pytest_cache
```

To clean LaTeX artifacts:
```bash
rm -rf LaTeX/build/* LaTeX/logs/* LaTeX/pdfs/*
```

### Manual single-file compilation

```bash
cd LaTeX/src/example
latexmk -pdf -shell-escape -interaction=nonstopmode -halt-on-error light_main.tex
```

---

## File Naming Conventions

These conventions are **critical** — `run.py` relies on them to locate and move files.

| Pattern | Meaning |
|---------|---------|
| `*_main.tex` | Compilation target — `run.py` finds and compiles these |
| `*_content.tex` | Document body (tables of contents, section files) |
| Other `*.tex` files | Partials included via `\input{}` |

### Exclude patterns

Files containing any of these substrings in their path are **ignored** by `run.py`:
```
legacy, templates, tmp, temp
```

### Output mirroring

`run.py` mirrors the `src/` directory structure into `build/`, `pdfs/`, and `logs/`:

```
src/ch1/sec1/chapter_main.tex  →  build/ch1/sec1/, pdfs/ch1/sec1/, logs/ch1/sec1/
```

Global assets (images, etc.) should live under `src/assets/` so they are accessible via relative paths.

Local assets can be included in the same directory as the `*_main.tex` file, but in a local `assets/` subfolder to avoid cluttering the main directory.

---

## Header Load Order

Headers **must** be loaded in this exact order. Some depend on others being loaded first.

### In `*_main.tex` files — in this order:

1. `base_header.tex`
2. `theme_choice_*.tex`
3. `theme.tex`

### In `*_content.tex` files — in this order:

4. `math/base.tex`
5. `math/advanced.tex`
6. `environments/base.tex`
7. `tikz.tex`
8. `algorithms/base.tex`
9. `algorithms/advanced.tex`
10. `language/*your_language*.tex`
11. `themes/more_colours.tex`

See `LaTeX/src/example/example_content.tex` for the full reference.

---

## LaTeX Code Style

- **Always use `\input{}`** for including files — never `\include{}` (it forces a page break and requires `\includeonly{}` management).
- **Use `\ref{}` / `\eqref{}`** for referencing sections, equations, etc. — never hardcoded numbers.
- **Custom commands** are defined in `base_header.tex`. Use `\providecommand` to avoid redefining errors.
- **Custom environments**: copy `environments/template.tex` into your own header and modify it — do not load `template.tex` directly.
- **Class**: use `\documentclass[french,a4paper,10pt]{article}` as the base.
- **Language**: `babel` is loaded with `[french]` in `base_header.tex`.

---

## Gitignore

```
LaTeX/build/
LaTeX/logs/
```

`pdfs/` is intentionally **not** gitignored so compiled output can be committed if desired.

---

## Git Workflow

```bash
git checkout -b feature/feature-name
git add .
git commit -m "description"
git push -u origin feature/feature-name
```

### Commit message format

```
type: short description
```

Types: `feat`, `fix`, `refactor`, `docs`, `chore`
