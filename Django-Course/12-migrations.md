# 12. Migrations

> Migrations are Django's way of turning model changes into database changes.

## Quick Map

| Part | Meaning |
|---|---|
| `makemigrations` | create migration file |
| `migrate` | apply changes to DB |
| `showmigrations` | list migrations |

## 1) What is a Migration?

A migration records changes in your models so Django can update the database safely.

```text
Model change -> Migration file -> Database update
```

## 2) Migration Commands

| Command | Use |
|---|---|
| `makemigrations` | detect model changes |
| `migrate` | apply changes |
| `showmigrations` | check status |

## 3) Migration Workflow

1. Edit model.
2. Run `makemigrations`.
3. Review the migration file.
4. Run `migrate`.

### Small Shape

```text
models.py -> migrations/*.py -> database
```

## 4) Common Points

- Changing a field creates a new migration.
- Migrations can be reversed if needed.
- Bad migration order can cause errors.

## Why It Matters in Django

- Migrations keep code and database in sync.
- They are essential for team development and production deployment.
- Interview questions often test the migration workflow.
