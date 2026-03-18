# CLAUDE.md

## Project Overview

This project generates professional CV/resume PDFs from YAML data files using Python and ReportLab. It is a personal CV generator for Conor Cosnett. The YAML files define the CV content (name, contact info, work experience, education, etc.) and `main.py` renders them into styled PDF documents.

## Key Files

- **`inputs/cv_min.yaml`** — The primary CV definition file. This is the most important file in the project. It describes the CV content in a structured YAML format.
- **`inputs/`** — Directory containing multiple YAML CV variants (different versions tailored for different purposes). Each YAML file produces a corresponding PDF.
- **`main.py`** — The main entry point. Reads all YAML files from `inputs/`, processes each one, and generates PDFs in `outputs/`. Contains all PDF layout/styling logic (fonts, margins, paragraph styles, header rendering).
- **`utils.py`** — Helper functions for rendering bullet points, simple text, and markdown-to-ReportLab conversion (bold via `**`, links, line breaks).
- **`layout.txt`** — Legacy/alternative layout module (mostly commented out). Not actively used by `main.py`.
- **`outputs/`** — Generated PDF files (gitignored).
- **`logos/`** — Company/institution logo images referenced in the YAML files.
- **`resources/`** — Icon images for contact info (email, phone, GitHub, LinkedIn).
- **`calibri*.ttf`** — Calibri font files registered and used for PDF rendering.
- **`docs_for_claude/`** — Reference screenshots showing example CV output for visual guidance.
- **`format.sh`** — Runs `flake8`, `isort`, and `black` for linting/formatting.

## YAML CV Schema

Each YAML file in `inputs/` follows this structure:

```yaml
name: "Full Name"
email: "email@example.com"
linkedin: "linkedin-handle"
github: "github-handle"
# Optional: phone, website, headline, prompt

sections:
  - heading: "Section Title"        # e.g. "Work Experience", "Education"
    logos: [...]                     # Optional list of logo paths for the section
    subsections:                    # Optional list of subsections
      - heading:
          logo: /logo_file.png      # Optional logo
          label: "Role - Company"   # Job title / degree
          startdate: "Start Date"
          enddate: "End Date"
        points:                     # Bullet points (list of strings)
          - "Achievement or responsibility"
        degrees:                    # Alternative to points, for education
          - "Degree details"
        text: |                     # Alternative to points, for paragraph text
          Free-form paragraph text with optional markdown.
    points:                         # Section-level bullet points (e.g. for Skills)
      - "**Category**: item1, item2"
    text: "Section-level text"      # Section-level paragraph text
```

Text content supports limited markup: `**bold**`, HTML links (`<a href="..."><u>text</u></a>`), and double-space for line breaks.

## Dependencies

- **Python >= 3.12**
- **reportlab 4.1.0** — The core PDF generation library. Provides `SimpleDocTemplate`, `Paragraph`, `Canvas`, flowables (`HRFlowable`, `Spacer`, `Image`), and font registration (`TTFont`). This is the primary dependency that powers all PDF creation.
- **PyYAML 6.0.1** — Parses the YAML CV definition files using `yaml.safe_load()`.
- **uv** — Used as the package manager (see `uv.lock` and `pyproject.toml`). Install deps with `uv sync`.

## How to Build

```bash
# Install dependencies
uv sync

# Generate all CVs
python main.py
```

This reads every `.yaml` file in `inputs/` and writes corresponding PDFs to `outputs/`.

## Formatting / Linting

```bash
bash format.sh
```

Runs `flake8` (linting), `isort` (import sorting), and `black` (code formatting).

## Architecture Notes

- `main.py` registers Calibri font variants (regular, bold, italic, bold-italic) and defines multiple `ParagraphStyle` objects for different CV elements (title, section headings, subsection headings, dates, bullet points).
- The `process_file()` function in `main.py` is the core rendering pipeline: it parses a YAML file, builds a list of ReportLab flowable objects (Paragraphs, HRFlowables, Spacers), and calls `document.build()` to produce the PDF.
- `utils.py` provides `bullet_points()` for rendering bulleted lists and `markdown_replace()` for converting markdown-style bold and links to ReportLab's XML-based markup.
- The project runs from the repo root directory (font paths and resource paths are relative to it).

## Important Conventions

- Always run `main.py` from the project root directory since font and resource paths are relative.
- When editing CV content, only modify files in `inputs/`. The primary CV is `inputs/cv_min.yaml`.
- Logo paths in YAML files use `/logo_name.png` format — these resolve to the `logos/` directory.
- The `outputs/` directory is gitignored; PDFs are generated locally.
