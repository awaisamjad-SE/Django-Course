# 23. Asynchronous Django Topics

> Async Django helps handle waiting work more efficiently.

## Quick Map

| Part | Meaning |
|---|---|
| Async view | non-blocking view |
| Async middleware | async request layer |
| WebSocket | real-time channel |
| Channel layer | message transport |

## 1) Async Support

Async views are useful when your app spends a lot of time waiting on I/O.

```text
Request -> await -> Response
```

## 2) Async Middleware Basics

Middleware can also work in async flow when needed.

## 3) ASGI Ecosystem

| Topic | Meaning |
|---|---|
| WebSocket | live communication |
| Real-time updates | instant changes |
| Channel layer | move messages between workers |

## Why It Matters in Django

- Async is important for real-time and high-concurrency apps.
- Django now supports both sync and async styles.
