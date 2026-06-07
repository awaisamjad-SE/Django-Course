# 18. Testing in Django

> Testing checks that your models, views, forms, and APIs work correctly.

## Quick Map

| Part | Meaning |
|---|---|
| Unit test | test one small piece |
| Integration test | test pieces together |
| `TestCase` | Django test class |
| `Client` | simulate browser request |
| `RequestFactory` | build custom request |

## 1) Testing Basics

```text
Write test -> Run test -> Fix bug -> Repeat
```

## 2) Django Test Tools

| Tool | Use |
|---|---|
| `TestCase` | test DB-backed code |
| `Client` | test HTTP requests |
| `RequestFactory` | create request object |

## 3) What to Test

- Models
- Views
- Forms
- APIs

## 4) Why Testing Matters

Testing helps catch bugs early and makes refactoring safer.

## Why It Matters in Django

- Django has a strong built-in testing system.
- Interviewers often ask about test types and tools.
- Good tests improve project quality and confidence.
