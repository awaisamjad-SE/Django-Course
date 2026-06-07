# 14. Authentication and Authorization

> Authentication asks who the user is. Authorization asks what the user is allowed to do.

## Quick Map

| Part | Meaning |
|---|---|
| Authentication | identify user |
| Authorization | check permissions |
| Groups | bundle permissions |
| Custom user | change user model |
| JWT | token-based auth |

## 1) Authentication

Common pieces:

- Login
- Logout
- `request.user`
- Session-based authentication

```text
Login -> user identified -> request.user available
```

## 2) Authorization

| Part | Meaning |
|---|---|
| Permissions | allow specific actions |
| Groups | collection of permissions |
| RBAC | role-based access control |

## 3) User Models

| Type | Meaning |
|---|---|
| Default user model | Django's built-in user |
| Custom user model | your own user structure |
| Custom manager | helper for user creation |

## 4) Security and Auth Concepts

- Password hashing
- Password reset flow
- Account verification basics

## 5) JWT Overview

| Token | Use |
|---|---|
| Access token | short-lived request auth |
| Refresh token | get new access token |
| Expiry | limits token life |

## Why It Matters in Django

- Authentication and permissions are core Django features.
- Most real apps need custom user handling or token auth.
- Interviewers often ask auth flow and JWT basics.
