# 17. Django Security

> Django includes strong security features to protect your app from common attacks.

## Quick Map

| Topic | Meaning |
|---|---|
| CSRF | protect forms |
| XSS | protect scripts |
| SQL injection | protect queries |
| Clickjacking | protect UI frames |
| Secret key | keep app secret |

## 1) Common Security Topics

| Threat | What It Tries To Do |
|---|---|
| CSRF | send unwanted form requests |
| XSS | inject harmful script |
| SQL injection | break database queries |
| Clickjacking | trick user clicks |

## 2) Secure Settings

| Setting | Use |
|---|---|
| `DEBUG` | should be `False` in production |
| `ALLOWED_HOSTS` | restrict domains |
| secret key | keep private |
| environment variables | store sensitive values |

## 3) File and Upload Safety

- Validate uploaded files.
- Restrict file types.
- Avoid unsafe user input.

```text
User input -> Validate -> Save safely
```

## Why It Matters in Django

- Security is built into Django, but you still need correct settings.
- Most production bugs come from unsafe deployment or weak input handling.
