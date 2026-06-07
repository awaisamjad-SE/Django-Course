# 05. Database Fundamentals

> Django sits on top of a database. If SQL is clear, the Django ORM becomes much easier to understand.

## 📌 Quick Map

| Area | Main Idea |
|---|---|
| SQL basics | read and edit data |
| Joins | combine tables |
| Constraints | protect data |
| Indexing | speed up search |
| Transactions | keep changes safe |

## 1) SQL Basics

| Command | Use |
|---|---|
| SELECT | read data |
| INSERT | add data |
| UPDATE | change data |
| DELETE | remove data |

### Mini Table

```text
users
+----+------+
| id | name |
+----+------+
| 1  | Ali  |
| 2  | Sara |
```

## 2) Joins

Joins combine rows from more than one table.

| Join | Meaning |
|---|---|
| INNER JOIN | matching rows only |
| LEFT JOIN | all left rows + matches |
| RIGHT JOIN | all right rows + matches |
| FULL JOIN | all rows from both sides |

```text
Users + Orders -> combined result
```

## 3) Constraints

| Constraint | Purpose |
|---|---|
| Primary key | unique row id |
| Foreign key | link between tables |
| Unique key | no duplicate values |
| Not null | value required |
| Check | rule validation |

## 4) Indexing

Indexing makes lookups faster.

```text
Without index -> search every row
With index    -> jump to likely rows
```

## 5) Transactions and ACID

| Property | Meaning |
|---|---|
| Atomicity | all or nothing |
| Consistency | valid state |
| Isolation | safe from conflicts |
| Durability | saved after commit |

## Why It Matters in Django

- ORM queries are SQL underneath.
- Model relationships depend on joins.
- Bad query design causes slow apps.
