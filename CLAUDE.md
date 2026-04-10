# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project context

This is a learning project — the goal is to learn the Django framework by following the "SaaS Foundations" tutorial:
- Video: https://www.youtube.com/watch?v=WbNNESIxJnY&t=499s
- Source: https://github.com/codingforentrepreneurs/SaaS-Foundations

The tutorial is somewhat outdated. Provide help **only when something doesn't work correctly** — focus on diagnosing the broken functionality and finding a fix compatible with current library versions, rather than proactively suggesting improvements or refactors.

## Coding style

- Prefer FBVs (function-based views) for simple logic, CBVs for complex views.
- Keep business logic in models and forms; keep views light.
- Use Django ORM — avoid raw SQL unless necessary.
- Use `select_related` / `prefetch_related` when fetching related objects.

## Commands

All Django commands run from `src/` directory (where `manage.py` lives).

```bash
# Run dev server
cd src && python manage.py runserver

# Apply migrations
cd src && python manage.py migrate

# Create migrations
cd src && python manage.py makemigrations

# Download vendor static files (Flowbite) into staticfiles/vendors/
cd src && python manage.py vendor_pull

# Collect static files
cd src && python manage.py collectstatic --noinput

# Run tests
cd src && python manage.py test

# Run a single test
cd src && python manage.py test visits.tests
```

## Environment

Requires a `.env` file in the project root (next to `src/`). Required variables:
- `DJANGO_SECRET_KEY`
- `DJANGO_DEBUG` (True/False)

Optional: `DATABASE_URL` (defaults to SQLite at `src/db.sqlite3`), email config (`EMAIL_HOST_USER`, `EMAIL_HOST_PASSWORD`), `ADMIN_USER_NAME`, `ADMIN_USER_EMAIL`.

## Architecture

The Django project lives entirely in `src/`. The main project package is `saas_project` (settings, root URLs, shared views).

**Apps:**
- `visits` — tracks page visits via `PageVisit` model (path + timestamp). Used by `about_view` to count hits per path.
- `profiles` — user profiles; `profile_view` uses `has_perm()` checks. `profiles/models.py` is currently empty (model not yet added).
- `auth` — custom login/register views (manual auth, not allauth). Parallel to allauth — both exist simultaneously.
- `commando` — management commands only. `vendor_pull` downloads Flowbite CSS/JS into `staticfiles/vendors/`.

**Authentication:** Two systems coexist:
1. Custom views at `/login/` and `/register/` (`auth/views.py`) — basic username/password.
2. `django-allauth` at `/accounts/` — supports email login, email verification, and GitHub OAuth (`allauth.socialaccount.providers.github`).

**Static files flow:**
1. `vendor_pull` downloads third-party files to `src/staticfiles/vendors/`
2. `STATICFILES_DIRS` points to `src/staticfiles/`
3. `collectstatic` outputs to `src/local-cdn/` (served via WhiteNoise in production)

**Templates:** Global templates in `src/templates/`. All pages extend `base.html`, which includes `base/css.html` (Flowbite + custom.css), `nav/navbar.html`, `base/messages.html`, and `base/js.html`. Allauth templates are overridden in `src/templates/allauth/`.

**Deployment:** Railway via Dockerfile. Build runs `vendor_pull` + `collectstatic`. Runtime script runs `migrate` then `gunicorn`. Production DB is PostgreSQL via `DATABASE_URL`.
