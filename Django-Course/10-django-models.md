# 10. Django Models

> Models define the database structure in Python.

## 📌 Quick Map

| Topic | Meaning |
|---|---|
| Model | database table |
| Field | table column |
| Relationship | link between tables |
| Manager | query helper |

## 1) What is a Model?

A model is a Python class that maps to a database table.

```python
class Student(models.Model):
    name = models.CharField(max_length=100)
```

```text
Model class -> Database table
```

## 2) Fields

| Field | Use |
|---|---|
| `CharField` | short text |
| `TextField` | long text |
| `IntegerField` | number |
| `BooleanField` | true/false |
| `DateTimeField` | time value |

## 3) Meta Class

`Meta` controls things like ordering and table name.

## 4) Relationships

| Relationship | Meaning |
|---|---|
| One-to-one | one record to one record |
| One-to-many | one parent, many children |
| Many-to-many | many records on both sides |

```text
Author 1 --- * Book
Student * --- * Course
```

## 5) `related_name`

It gives a friendly reverse lookup name.

## 6) Model Methods and Managers

| Method/Tool | Purpose |
|---|---|
| `__str__()` | readable text |
| `save()` | custom save logic |
| `clean()` | validation |
| Manager | custom queries |

```text
Model -> Manager -> QuerySet -> Data
```

## Why It Matters in Django

- Models are the backbone of Django apps.
- ORM, admin, forms, and DRF all depend on them.
