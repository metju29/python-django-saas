# SaaS Django Project

This is a Django-based SaaS foundation project, following modern practices for building scalable web applications.

## Prerequisites

- Python 3.10+
- Virtualenv
- Git

## Getting Started

### 1. Clone the repository

```bash
git clone <repository-url>
cd saas-django
```

### 2. Set up a Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Environment Configuration

Create a `.env` file in the root directory (same level as `src/`) and add the following required variables:

```env
DJANGO_SECRET_KEY=your-secret-key-here
DJANGO_DEBUG=True
```

Optional variables:
- `DATABASE_URL`: (Defaults to SQLite at `src/db.sqlite3`)
- `EMAIL_HOST_USER`: For SMTP email support
- `EMAIL_HOST_PASSWORD`: For SMTP email support
- `ADMIN_USER_NAME`: Default admin name
- `ADMIN_USER_EMAIL`: Default admin email

### 5. Pull Vendor Static Files

This project uses Flowbite and other vendor assets. Run the custom management command to download them:

```bash
cd src
python manage.py vendor_pull
```

### 6. Run Migrations

```bash
python manage.py migrate
```

### 7. Start the Development Server

```bash
python manage.py runserver
```

The application will be available at `http://127.0.0.1:8000/`.

## Key Management Commands

All commands should be run from the `src/` directory:

- **Create migrations:** `python manage.py makemigrations`
- **Collect static files:** `python manage.py collectstatic --noinput`
- **Run tests:** `python manage.py test`
- **Create superuser:** `python manage.py createsuperuser`

## Project Structure

- `src/saas_project/`: Main project configuration (settings, URLs).
- `src/templates/`: Global HTML templates.
- `src/staticfiles/`: Static assets (CSS, JS, Images).
- `src/visits/`: App for tracking page visits.
- `src/profiles/`: User profile management.
- `src/commando/`: Custom management commands (e.g., `vendor_pull`).

## Authentication

The project uses two authentication systems:
1. **Custom Auth:** Manual login/register views at `/login/` and `/register/`.
2. **Django Allauth:** Robust authentication system at `/accounts/` supporting social logins (GitHub) and email verification.

## Deployment

The project is configured for deployment on **Railway** using the included `Dockerfile` and `railway.toml`.
