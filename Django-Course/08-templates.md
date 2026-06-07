# 08. Templates

> Templates turn data into HTML pages.

## 📌 Quick Map

| Part | Purpose |
|---|---|
| Template engine | renders HTML |
| Context | data from view |
| Tags | control logic |
| Filters | format output |

## 1) Template Engine

```python
return render(request, "home.html", {"name": "Awais"})
```

```text
Data + Template -> Final HTML
```

## 2) Template Tags

| Tag | Meaning |
|---|---|
| `if` | condition |
| `for` | loop |
| `block` | replace section |
| `extends` | inherit layout |
| `include` | reuse file |

## 3) Basic Layout

```text
base.html
  ├─ header
  ├─ body
  └─ footer
```

## 4) Template Filters

Examples:

- `upper`
- `lower`
- `length`
- `date`

## 5) Context Data

```python
context = {"title": "Dashboard", "count": 5}
```

## Why It Matters in Django

- Templates build server-rendered HTML.
- They keep design separate from Python logic.
- Reuse becomes easier with blocks and includes.
