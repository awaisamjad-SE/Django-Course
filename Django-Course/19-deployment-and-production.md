# 19. Deployment and Production

> Deployment is the process of putting your Django app on a real server for users.

## Quick Map

| Part | Meaning |
|---|---|
| Web server | receives user requests |
| App server | runs Django app |
| Reverse proxy | forwards traffic |
| Database server | stores data |
| Environment variables | store settings safely |

## 1) Production Setup

| Topic | Meaning |
|---|---|
| `DEBUG = False` | production mode |
| allowed hosts | restrict domains |
| static files | collected for serving |
| media files | user uploads |

## 2) Deployment Components

```text
Browser -> Web server -> App server -> Django -> Database
```

## 3) Common Topics

- Environment variables
- Logging
- Error monitoring
- Caching

## 4) Why It Matters

Production is different from development. Settings, file handling, and performance all matter more.

## Why It Matters in Django

- A working local project is not enough.
- Deployment questions are common in interviews.
- You must know how Django behaves outside the dev server.
