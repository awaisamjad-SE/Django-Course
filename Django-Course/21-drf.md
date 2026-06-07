# 21. Django REST Framework (DRF)

> DRF helps you build APIs on top of Django.

## Quick Map

| Part | Meaning |
|---|---|
| REST | API style |
| Serializer | convert data |
| ViewSet | API logic holder |
| Router | auto URL mapping |
| Permission | access control |

## 1) REST Basics

REST uses resource-based URLs and HTTP methods like GET, POST, PUT, and DELETE.

```text
/users/ -> list or create
/users/1/ -> detail, update, delete
```

## 2) Serializers

Serializers convert Python objects into JSON and validate incoming data.

| Serializer Type | Use |
|---|---|
| `Serializer` | manual fields |
| `ModelSerializer` | model-based |

## 3) Views and Routers

| Part | Meaning |
|---|---|
| `APIView` | custom API view |
| `GenericAPIView` | reusable base |
| `ViewSet` | bundle CRUD actions |
| Router | creates URLs automatically |

## 4) Permissions and Authentication

| Topic | Meaning |
|---|---|
| Authentication | who the user is |
| Permission | what the user can do |
| Throttling | limit requests |

## 5) Pagination, Filtering, Exceptions

- Pagination splits large results.
- Filtering and search narrow results.
- Exception handling returns clean API errors.

## Why It Matters in Django

- DRF is the standard way to build JSON APIs with Django.
- Most modern backend apps need serializers, permissions, and routers.
