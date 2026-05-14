# Blog Management System

A RESTful API for a blog platform built with **FastAPI**, **SQLAlchemy**, and **MySQL**.

---

## Tech Stack

- **FastAPI** — web framework
- **SQLAlchemy** — ORM
- **MySQL** (via PyMySQL) — database
- **Passlib / bcrypt** — password hashing
- **python-jose** — JWT authentication
- **Redis** — caching client
- **Uvicorn** — ASGI server

---

## Project Structure

```
Blog-Management-System/
├── app/
│   ├── main.py               # FastAPI app entry point, middleware, routes
│   ├── database.py           # SQLAlchemy engine & session setup
│   ├── monitoring.py         # Request/error counters
│   ├── redis_client.py       # Redis connection
│   │
│   ├── auth/
│   │   ├── hashing.py        # Password hashing & verification
│   │   └── jwt_handler.py    # JWT token creation & verification
│   │
│   ├── models/
│   │   ├── user.py           # User SQLAlchemy model
│   │   ├── post.py           # Post SQLAlchemy model
│   │   └── comment.py        # Comment SQLAlchemy model (with replies)
│   │
│   ├── schemas/
│   │   ├── user_schema.py    # Pydantic schemas for User
│   │   ├── post_schema.py    # Pydantic schemas for Post
│   │   └── comment_schema.py # Pydantic schemas for Comment
│   │
│   ├── routes/
│   │   ├── user_routes.py    # Auth endpoints (register, login)
│   │   ├── post_routes.py    # CRUD endpoints for posts
│   │   └── comment_routes.py # CRUD endpoints for comments & replies
│   │
│   └── utils/
│       └── dependencies.py   # DB session dependency, JWT auth guard
│
└── requirements.txt
```

---

## File Breakdown (6 Sections)

| # | Folder/File | Responsibility |
|---|------------|----------------|
| 1 | `app/main.py` + `database.py` + `monitoring.py` + `redis_client.py` | App setup, DB connection, logging middleware, metrics |
| 2 | `app/auth/` | JWT token handling and bcrypt password hashing |
| 3 | `app/models/` | Database table definitions (User, Post, Comment) |
| 4 | `app/schemas/` | Request/response validation via Pydantic |
| 5 | `app/routes/` | API endpoints for users, posts, and comments |
| 6 | `app/utils/` | Shared dependencies: DB session, current-user auth guard |

---

## Setup & Run

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Configure the database

Edit `app/database.py` and update the connection string:

```python
DATABASE_URL = "mysql+pymysql://USER:PASSWORD@localhost/blog_system_db"
```

Create the MySQL database:

```sql
CREATE DATABASE blog_system_db;
```

### 3. Run the server

```bash
uvicorn app.main:app --reload
```

The API will be available at `http://localhost:8000`.

---

## API Endpoints

| Method | Path | Description | Auth Required |
|--------|------|-------------|---------------|
| GET | `/` | Health check | No |
| GET | `/metrics` | Request/error stats | No |
| POST | `/register` | Create a new user | No |
| POST | `/login` | Get JWT access token | No |
| GET | `/posts` | List all posts | No |
| POST | `/posts` | Create a post | Yes |
| GET | `/posts/{id}` | Get a single post | No |
| PUT | `/posts/{id}` | Update a post | Yes (owner) |
| DELETE | `/posts/{id}` | Delete a post | Yes (owner) |
| POST | `/posts/{id}/comments` | Add a comment | Yes |
| PUT | `/comments/{id}` | Update a comment | Yes (owner) |
| DELETE | `/comments/{id}` | Delete a comment | Yes (owner) |

---

## Authentication

The API uses **OAuth2 Bearer tokens**. After logging in via `/login`, include the token in subsequent requests:

```
Authorization: Bearer <your_token>
```

---

## User Roles

- `reader` — can view posts and add comments
- `author` — can create, edit, and delete their own posts

---

## Monitoring

Visit `/metrics` to see live stats:

```json
{
  "request_count": 42,
  "error_count": 1,
  "average_response_time": 0.023,
  "system_status": "healthy"
}
```
