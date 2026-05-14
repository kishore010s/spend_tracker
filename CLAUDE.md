# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Spendly** is an educational personal expense tracking web app where students progressively implement authentication, database operations, and expense management. The `prompt.txt` file contains the full step-by-step learning guide.

## Commands

```bash
# Setup
python -m venv venv
venv\Scripts\activate        # Windows
pip install -r requirements.txt

# Run development server (http://localhost:5001)
python app.py

# Run tests
pytest
pytest -v                    # verbose
pytest tests/test_auth.py    # specific file
```

## Architecture

**Stack**: Flask 3.1.3 + Jinja2 templates + vanilla CSS + vanilla JS + SQLite

### Backend (`app.py`)
Single-file Flask app with all routes. Current routes: `/`, `/register`, `/login`, `/terms`, `/privacy`. Placeholder routes exist for future student work: `/logout`, `/profile`, `/expenses/*`.

### Database (`database/db.py`)
Student-implemented. Must provide `get_db()` (SQLite connection with row_factory + PRAGMA foreign_keys), `init_db()` (schema creation), and `seed_db()` (sample data).

### Templates (`templates/`)
All templates extend `base.html`, which provides navbar and footer. Base defines blocks: `title`, `head`, `content`, `scripts`.

```html
{% extends "base.html" %}
{% block title %}Page Title{% endblock %}
{% block content %}<!-- content here -->{% endblock %}
{% block scripts %}<script>...</script>{% endblock %}
```

### Styling (`static/css/`)
No CSS framework — custom CSS with design tokens as CSS variables in `:root`:
- **Colors**: `--ink`, `--paper`, `--accent` (green), `--accent-2` (orange), `--danger`
- **Fonts**: DM Serif Display (headings), DM Sans (body)
- **Radius**: `--radius-sm` (6px), `--radius-md` (12px), `--radius-lg` (20px)

`style.css` is global; `landing.css` is landing-page-only. Component class prefixes: `.lp-` (landing), `.auth-` (auth pages), `.legal-` (terms/privacy).

### JavaScript (`static/js/main.js`)
Vanilla JS only — no framework. Pattern: IIFE for scope isolation (see modal in `landing.html` for reference implementation).

## Key Conventions

- Use `url_for()` for all internal links: `href="{{ url_for('route_name') }}"`
- Forms use standard HTML `POST`, no AJAX in base implementation
- Jinja2 conditionals for error display: `{% if error %}<div class="form-error">{{ error }}</div>{% endif %}`
- Currency is ₹ (Indian Rupee); locale context is Indian
- CSS variable usage is required for colors/radii — no hardcoded hex values
