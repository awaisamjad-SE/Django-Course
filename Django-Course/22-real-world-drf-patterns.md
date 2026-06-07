# 22. Real-World DRF Patterns

> These are common API patterns used in real projects.

## Quick Map

| Pattern | Meaning |
|---|---|
| CRUD | create, read, update, delete |
| Nested API | parent-child data |
| File upload | send files in API |
| Auth API | login/signup/token flow |
| Business features | search, sort, pagination |

## 1) CRUD API Design

A basic API usually has list, detail, create, update, and delete endpoints.

```text
GET /items/      -> list
GET /items/1/    -> detail
POST /items/     -> create
PUT /items/1/    -> update
DELETE /items/1/ -> delete
```

## 2) Nested and Related APIs

Used when one resource belongs to another.

```text
Author -> Books
Order -> Items
Category -> Products
```

## 3) File Upload APIs

File upload APIs must handle multipart data and validate files.

## 4) Auth APIs

| Endpoint | Use |
|---|---|
| signup | create account |
| login | get token/session |
| refresh | renew token |
| password reset | recover account |
| profile | user info |

## 5) Common Business Features

- Search
- Sort
- Filter
- Pagination
- Soft delete
- Audit fields

## Why It Matters in Django

- Real APIs need more than basic CRUD.
- Interviewers often ask about nested serializers, uploads, and auth flows.
