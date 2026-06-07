# 20. Performance and Scalability

> Performance is about making your app fast. Scalability is about keeping it fast as users grow.

## Quick Map

| Topic | Meaning |
|---|---|
| Query optimization | fewer slow queries |
| Caching | store repeated results |
| Background work | run heavy tasks later |
| N+1 problem | too many queries |
| Bulk operations | handle many rows fast |

## 1) Query Optimization

| Idea | Meaning |
|---|---|
| reduce database hits | avoid repeated queries |
| use indexes wisely | speed up search |
| avoid N+1 | load related data properly |

```text
Many requests -> fewer queries -> faster response
```

## 2) Caching

| Type | Use |
|---|---|
| Per-view cache | cache full view |
| Low-level cache | cache small data |
| Template fragment cache | cache part of page |

## 3) Background Work

Heavy work should move outside the request cycle.

- Celery basics
- Task queues
- Scheduled jobs

## Why It Matters in Django

- Fast queries and caching improve user experience.
- Interviewers often ask about N+1 and optimization.
- Real apps need performance planning as they grow.
