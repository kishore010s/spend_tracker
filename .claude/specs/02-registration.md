# Spec: Registration

## Overview
Implement user registration and login so that new users can create a Spendly account
and returning users can sign in. This step wires up the POST handlers for the two auth
routes that already render their templates, adds Flask session management, and provides
basic server-side validation with inline error messages. It is the first step where the
database is exercised by real user actions.

## Depends on
- Step 01 — Database setup (`database/db.py` with `get_db()`, `init_db()`, `seed_db()`,
  and the `users` table)

## Routes
- `GET  /register` — render registration form — public (already exists, no change)
- `POST /register` — validate form, insert user, redirect to `/login` — public
- `GET  /login`    — render login form — public (already exists, no change)
- `POST /login`    — validate credentials, set session, redirect to `/expenses` — public

## Database changes
No database changes. The `users` table created in Step 01 already has all required
columns (`id`, `name`, `email`, `password_hash`, `created_at`).

## Templates
- **Modify:** `templates/register.html` — template already exists and already contains
  the `{% if error %}` block and the POST form; no structural changes needed.
- **Modify:** `templates/login.html` — same situation; re-render with `error` on bad
  credentials; no structural changes needed.

## Files to change
- `app.py` — primary file for this step:
  - Add `secret_key` to the Flask app (required for `session`)
  - Extend imports: `request`, `session`, `redirect`, `url_for` from `flask`;
    `generate_password_hash`, `check_password_hash` from `werkzeug.security`
  - Convert `/register` to accept both GET and POST; add POST logic
  - Convert `/login` to accept both GET and POST; add POST logic

## Files to create
None.

## New dependencies
No new dependencies. `werkzeug` is already installed as a Flask dependency.

## Rules for implementation
- No SQLAlchemy or ORMs — use `get_db()` and raw SQL only
- Parameterised queries only — never use string formatting in SQL
- Passwords hashed with `werkzeug.security.generate_password_hash`; verified with
  `check_password_hash`
- Use CSS variables — never hardcode hex values in any template or stylesheet
- All templates extend `base.html`
- `app.secret_key` must be set before any session usage; use a hard-coded dev string
  for now (e.g. `"spendly-dev-secret"`) — a comment should note it must be replaced
  before production
- Session key for the logged-in user: `session["user_id"]` (integer) and
  `session["user_name"]` (string)
- Registration validation rules (check in order, return the first failure as `error`):
  1. All three fields (`name`, `email`, `password`) must be non-empty
  2. Password must be at least 8 characters
  3. Email must not already exist in `users` (catch the `UNIQUE` constraint or
     pre-check with a SELECT)
- On successful registration → redirect to `url_for('login')`
- On successful login → redirect to `url_for('landing')` (placeholder until the
  expenses dashboard exists in a later step)
- On failed login → re-render `login.html` with `error="Invalid email or password"`
  (never reveal which field was wrong)

## Definition of done
- [ ] Visiting `/register` renders the form (GET still works)
- [ ] Submitting the form with all valid fields creates a new row in `users` and
      redirects to `/login`
- [ ] Submitting with an empty field re-renders the form with an inline error message
- [ ] Submitting with a password shorter than 8 characters shows a password-length error
- [ ] Submitting with a duplicate email shows a "email already registered" error
- [ ] Visiting `/login` renders the login form (GET still works)
- [ ] Logging in with the demo user (`demo@spendly.com` / `demo123`) succeeds and
      redirects away from `/login`
- [ ] Logging in with a newly registered account succeeds
- [ ] Logging in with a wrong password shows the generic "Invalid email or password" error
- [ ] After login, `session["user_id"]` and `session["user_name"]` are set
- [ ] App starts without errors
