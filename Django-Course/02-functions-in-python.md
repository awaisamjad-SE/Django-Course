# 02. Functions in Python

> Functions are reusable blocks of code. Django uses them in views, validators, helpers, and serializer logic.

## 📌 Quick Map

| Term | Meaning |
|---|---|
| Function | reusable block |
| Parameter | placeholder in function |
| Argument | actual value passed in |
| Return | output from function |

## 1) What is a Function?

A function is a named piece of code you can call again and again.

```python
def greet(name):
    return f"Hello, {name}"
```

```text
Input -> Function -> Output
```

## 2) Function Arguments

| Type | Example |
|---|---|
| Positional | `add(2, 3)` |
| Keyword | `add(a=2, b=3)` |
| Default | `greet("Ali")` or `greet()` |

```python
def add(a, b):
    return a + b
```

## 3) `*args` and `**kwargs`

| Syntax | Meaning |
|---|---|
| `*args` | many positional values |
| `**kwargs` | many keyword values |

```python
def demo(*args, **kwargs):
    print(args)
    print(kwargs)
```

### Shape

```text
args   -> (1, 2, 3)
kwargs -> {"x": 10, "y": 20}
```

## 4) Scope

Scope tells Python where a variable can be used.

| Scope | Where it lives |
|---|---|
| Local | inside function |
| Global | outside function |
| nonlocal | inside nested function |

## 5) Lambda, map, filter, reduce

- Lambda = short anonymous function
- `map()` = transform items
- `filter()` = keep matching items
- `reduce()` = combine items

```python
square = lambda x: x * x
```

```text
[1, 2, 3] -> map(square) -> [1, 4, 9]
```

## Why It Matters in Django

- Views can be function-based.
- Validation often uses small helper functions.
- DRF and Django code both rely on reusable functions for clean structure.
