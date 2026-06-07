# 13. Forms and Validation

> Forms handle user input. Validation checks whether that input is safe and correct.

## Quick Map

| Part | Meaning |
|---|---|
| Form | collect input |
| ModelForm | form tied to model |
| Validation | check input |
| `clean()` | whole-form validation |
| `clean_<field>()` | field-level validation |

## 1) Django Forms

A form defines input fields and how data is collected.

| Form Part | Use |
|---|---|
| Form class | defines fields |
| Form rendering | shows fields on page |
| Form submission | receives data |

## 2) Model Forms

A `ModelForm` connects a form directly to a model.

```text
Form -> Model -> Database
```

## 3) Validation

| Type | Meaning |
|---|---|
| Field validation | checks one field |
| Form validation | checks full form |

Common methods:

- `clean()`
- `clean_<fieldname>()`

## 4) Why Validation Matters

Validation prevents bad data from entering the database. It also improves security and user experience.

## Why It Matters in Django

- Forms are common in login, signup, and data entry pages.
- Validation is used in both web forms and API serializers.
- Interviewers often ask the difference between `Form` and `ModelForm`.
