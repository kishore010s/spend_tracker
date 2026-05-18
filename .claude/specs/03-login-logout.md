# Spec: Login and Logout

## Overview
Implement the `/logout` route and make the entire UI session-aware. After Step 02, users
can register and sign in, but the navbar always shows "Sign in / Get started" regardless
of auth state, and hitting `/logout` returns a stub string. This step closes those gaps:
logout clears the Flask session, the navbar conditionally renders logged-in vs logged-out
controls, and visiting `/login` or `/register` while already signed in redirects away
rather than showing a redundant form.

## Depends on
- Step 01 — Database setup (`get_db()`, `users` table)
- Step 02 — Registration and login (working `/login` POST, `session["user_id"]` /
  `session["user_name"]` set on sign-in)

## Routes
- `GET /logout` — clear session, redirect to `/` — public (must be accessible to log out)

No other new routes. `/login` and `/register` gain redirect-if-logged-in guards but their
method signatures are unchanged.

## Database changes
No database changes.

## Templates
- **Modify:** `templates/base.html` — update `.nav-links` to branch on
  `session.get('user_id')`:
  - **Logged out** (current state): "Sign in" + "Get started" links
  - **Logged in**: greeting with `session['user_name']` + "Sign out" link to
    `url_for('logout')`

## Files to change
- `app.py` — two changes:
  1. Replace the `/logout` placeholder with a real implementation: call `session.clear()`,
     then `redirect(url_for('landing'))`
  2. Add redirect guards to `/login` and `/register` GET handlers: if
     `session.get('user_id')` is truthy, redirect to `url_for('landing')` immediately
- `templates/base.html` — session-aware navbar (see Templates section above)

## Files to create
None.

## New dependencies
No new dependencies.

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with `werkzeug`
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Use `session.clear()` (not `session.pop`) so any future session keys are also wiped
- Logout must be `GET /logout` (no form/CSRF needed at this learning stage)
- The navbar greeting must use `session['user_name']`, not a DB query
- Redirect guards apply to both GET of `/login` and GET of `/register` only — POST
  handlers do not need guards (a logged-in POST is harmless and rare)
- Do not introduce a `login_required` decorator in this step — that belongs with the
  profile/expenses steps

## Definition of done
- [ ] `GET /logout` clears the session and redirects to the landing page
- [ ] After logout, `session["user_id"]` and `session["user_name"]` are no longer set
- [ ] Navbar shows "Sign in" + "Get started" when no user is in session
- [ ] Navbar shows the user's name and a "Sign out" link when a user is logged in
- [ ] Clicking "Sign out" in the navbar logs the user out and the navbar reverts to
      logged-out state
- [ ] Visiting `/login` while already logged in redirects to the landing page (no form shown)
- [ ] Visiting `/register` while already logged in redirects to the landing page
- [ ] Logging in with `demo@spendly.com` / `demo123` shows the user's name in the navbar
- [ ] App starts without errors
