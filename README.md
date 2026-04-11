# Film Management System

A Django web application for uploading, storing, and streaming movies, backed by YugabyteDB — a PostgreSQL-compatible distributed database.

## Features

- Upload movie files with title and description
- Browse all uploaded movies in a list view
- Stream movies directly from the database via an embedded video player
- Django admin panel for database management

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Django 5.0.4, Django REST Framework |
| Database | YugabyteDB (PostgreSQL-compatible, port 5433) |
| Frontend | Django Templates, HTML5, CSS3 |
| Runtime | Python 3.x |

## Prerequisites

- Python 3.x
- [Docker](https://docs.docker.com/get-docker/) (for running YugabyteDB)

## Setup

### 1. Start YugabyteDB

Using the provided shell scripts to spin up a 3-node local cluster:

```shell
cd yugabytedb_docker_shellscript/
bash populate-local-nodes.sh
```

Alternatively, follow the [YugabyteDB Docker quick-start guide](https://docs.yugabyte.com/preview/quick-start/docker/).

The application expects YugabyteDB to be reachable at `localhost:5433` with the default credentials (`yugabyte`/`yugabyte`).

### 2. Install dependencies

```shell
pip3 install -Ur requirements.txt
```

### 3. Apply database migrations

```shell
python3 manage.py makemigrations
python3 manage.py migrate
```

### 4. (Optional) Create a superuser for the admin panel

```shell
python3 manage.py createsuperuser
```

### 5. Start the development server

```shell
python3 manage.py runserver
```

The application will be available at http://127.0.0.1:8000/.

## Pages & Routes

| URL | Description |
|-----|-------------|
| `/` | Homepage |
| `/add_movie/` | Upload a new movie |
| `/movies/` | Browse all uploaded movies |
| `/movie_item/<id>/` | Movie detail page with embedded video player |
| `/video/<id>/` | Raw video stream endpoint (MP4) |
| `/admin/` | Django admin interface |

## Database Schema

**Table: `movies`**

| Column | Type | Description |
|--------|------|-------------|
| `id` | BigAutoField (PK) | Auto-generated primary key |
| `movie_title` | VARCHAR(200) | Movie title |
| `file` | BYTEA | Binary video file content |
| `description` | TEXT (max 2000) | Movie description |

> Videos are stored as binary data directly in the database, enabling streaming without a separate file server.

## Project Structure

```
Film-Management-System/
├── manage.py
├── requirements.txt
├── yugabyteTest/                  # Django project configuration
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── testdb/                        # Main Django application
│   ├── models.py                  # Movie model
│   ├── views.py                   # View logic & video streaming
│   ├── forms.py                   # MovieForm
│   ├── urls.py                    # App-level URL routing
│   ├── admin.py                   # Admin registration
│   ├── templates/
│   │   ├── base.html
│   │   ├── index.html
│   │   ├── add_movie.html
│   │   ├── movies.html
│   │   ├── movie_item.html
│   │   └── partials/
│   │       ├── _header.html
│   │       └── _footer.html
│   └── static/
│       ├── css/style.css
│       └── img/film.jpg
└── yugabytedb_docker_shellscript/ # YugabyteDB Docker helper scripts
    ├── populate-local-nodes.sh
    ├── reconnect-loop-local-nodes.sh
    └── rm-populate-local-nodes.sh
```

## YugabyteDB Docker Scripts

| Script | Description |
|--------|-------------|
| `populate-local-nodes.sh` | Create a 3-node local YugabyteDB cluster |
| `reconnect-loop-local-nodes.sh` | Reconnect to an existing cluster |
| `rm-populate-local-nodes.sh` | Stop and remove the cluster containers |

## Screenshots

### Homepage
<img width="1648" alt="Homepage" src="https://github.com/I-Sheng/yugabyteTest/assets/91582709/8433b0c1-20cd-4dde-93b4-04b593b53e6d">

### Add Movie Page
<img width="1619" alt="Add Movie" src="https://github.com/I-Sheng/yugabyteTest/assets/91582709/5c302193-0c17-497d-b2c9-b19382813ec9">

### Movie List Page
<img width="1604" alt="Movie List" src="https://github.com/I-Sheng/yugabyteTest/assets/91582709/739a7b4f-5051-43d5-9107-92b07281334b">

## Notes

- `DEBUG = True` and `ALLOWED_HOSTS = []` are set for local development. Update `settings.py` before deploying to production.
- The secret key in `settings.py` should be replaced with a securely generated value for any non-local environment.
- Storing large binary files in the database is convenient for development but may impact performance at scale; consider migrating to object storage (e.g., S3) for production use.
