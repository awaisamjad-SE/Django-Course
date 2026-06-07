# 16. Django Admin

> Django Admin is the built-in dashboard for managing your database models.

## Quick Map

| Part | Meaning |
|---|---|
| Register model | show model in admin |
| List display | columns in table |
| Search fields | search box |
| Filters | side filters |
| Inline models | edit related records |

## 1) Admin Basics

You register a model so it appears in the admin panel.

```text
Model -> Admin site -> Manage data
```

## 2) Customization

| Option | Use |
|---|---|
| `list_display` | show fields in table |
| `search_fields` | search records |
| `list_filter` | filter records |
| `ordering` | sort rows |
| inlines | edit related objects |

## 3) Why It Matters

- Admin gives fast data management.
- It is useful for internal tools and quick debugging.
- Interviewers often ask how to customize admin views.
