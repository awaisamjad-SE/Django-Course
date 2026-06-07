# 11. Django ORM

> The ORM lets you work with the database using Python instead of writing SQL all the time.

## Quick Map

| Part | Meaning |
|---|---|
| QuerySet | database result set |
| Filter | search rows |
| Annotation | add calculated values |
| `select_related()` | follow single-valued relations |
| `prefetch_related()` | follow multi-valued relations |

## 1) CRUD Basics

| Action | ORM Method |
|---|---|
| Create | `create()`, `save()` |
| Read | `get()`, `filter()`, `all()` |
| Update | `update()` |
| Delete | `delete()` |

## 2) QuerySet Methods

Common methods:

- `first()`
- `last()`
- `exists()`
- `count()`
- `values()`
- `values_list()`
- `distinct()`

```text
QuerySet -> filter -> count / first / values
```

## 3) ORM Lookups

| Lookup | Use |
|---|---|
| `__icontains` | contains text, case-insensitive |
| `__startswith` | starts with |
| `__endswith` | ends with |
| `__in` | value in list |
| `__range` | between two values |

## 4) Query Building

- `Q` objects help combine conditions.
- `F` expressions compare model fields.
- `annotate()` adds calculated data.
- `Subquery` and `Exists` help with advanced queries.

### Simple Shape

```text
Model -> QuerySet -> Filter -> Result
```

## 5) Performance Optimization

| Method | Use |
|---|---|
| `select_related()` | follow foreign key / one-to-one |
| `prefetch_related()` | follow many-to-many / reverse relations |
| bulk operations | create or update many rows fast |

## Why It Matters in Django

- ORM is one of Django's main strengths.
- Good query design reduces slow pages and API delays.
- Interviewers often ask about N+1 queries and relation loading.
