# 15. Django Middleware, Sessions, and Signals

> These features help Django process requests, remember users, and react to model changes.

## Quick Map

| Part | Meaning |
|---|---|
| Middleware | request/response processing |
| Sessions | remember user data |
| Messages | flash notices |
| Signals | react to events |

## 1) Middleware

Middleware runs before and after views. It can inspect or change requests and responses.

```text
Request -> Middleware -> View -> Middleware -> Response
```

| Topic | Meaning |
|---|---|
| Middleware order | runs in sequence |
| Built-in middleware | Django provided |
| Custom middleware | your own logic |

## 2) Sessions

Sessions store data for a user across requests.

| Part | Meaning |
|---|---|
| Session storage | where data lives |
| Session settings | configuration |
| Session lifecycle | create, use, expire |

## 3) Messages Framework

Used for success, error, and flash messages after actions like login or form submit.

## 4) Signals

Signals let one part of the app react when something happens.

| Signal | Use |
|---|---|
| `pre_save` | before save |
| `post_save` | after save |
| `pre_delete` | before delete |
| `post_delete` | after delete |

## Why It Matters in Django

- Middleware controls global request behavior.
- Sessions support login state.
- Signals help with automated side effects like logs or notifications.
