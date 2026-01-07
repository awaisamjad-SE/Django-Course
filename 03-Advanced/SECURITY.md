# 🔒 DJANGO SECURITY - COMPLETE TUTORIAL A-Z

**Master Security Best Practices, CSRF, XSS, SQL Injection Prevention**

---

## 📖 TABLE OF CONTENTS

1. Security Fundamentals
2. CSRF Protection
3. XSS Prevention
4. SQL Injection Prevention
5. Password Security
6. Authentication Security
7. Authorization & Permissions
8. HTTPS & SSL/TLS
9. Secure Headers
10. Input Validation & Sanitization
11. File Upload Security
12. API Security
13. CORS Security
14. Rate Limiting & DDoS
15. Logging & Monitoring
16. Security Checklist
17. Interview Q&A
18. Quick Reference

---

# PART 1: SECURITY FUNDAMENTALS

## OWASP Top 10

```
1. Injection (SQL, NoSQL, etc.)
2. Authentication & Session Management
3. Cross-Site Scripting (XSS)
4. Broken Access Control
5. Security Misconfiguration
6. Sensitive Data Exposure
7. XML External Entities (XXE)
8. Broken Authentication
9. Using Components with Known Vulnerabilities
10. Insufficient Logging & Monitoring
```

---

## Security Layers

```
      User Input
          ↓
    Input Validation
          ↓
    Authentication
          ↓
    Authorization
          ↓
    Business Logic
          ↓
    Database
```

---

# PART 2: CSRF PROTECTION

## CSRF Token

```django
<form method="post">
    {% csrf_token %}
    <input type="text" name="username">
    <button type="submit">Submit</button>
</form>
```

---

## AJAX CSRF Protection

```javascript
// Get CSRF token
const token = document.querySelector('[name=csrfmiddlewaretoken]').value;

// Send with request
fetch('/api/endpoint/', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'X-CSRFToken': token
    },
    body: JSON.stringify({key: 'value'})
});
```

---

## CSRF Settings

```python
# settings.py
CSRF_COOKIE_SECURE = True      # HTTPS only
CSRF_COOKIE_HTTPONLY = False   # Allow JS access
CSRF_TRUSTED_ORIGINS = [
    'https://example.com'
]
```

---

# PART 3: XSS PREVENTION

## Template Auto-Escaping

```django
{# ✅ HTML is escaped by default #}
{{ user_input }}

{# ❌ Only use |safe for trusted content #}
{{ trusted_html|safe }}
```

---

## Django ORM Prevents Injection

```python
# ✅ Safe - parameterized query
Product.objects.filter(name=user_input)

# ❌ DANGEROUS - never do this!
Product.objects.raw(f"SELECT * FROM products WHERE name='{user_input}'")
```

---

## Content Security Policy

```python
# middleware.py
class CSPMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        response = self.get_response(request)
        response['Content-Security-Policy'] = (
            "default-src 'self'; "
            "script-src 'self' 'unsafe-inline'; "
            "style-src 'self' 'unsafe-inline'; "
            "img-src 'self' data: https:; "
        )
        return response
```

---

# PART 4: SQL INJECTION PREVENTION

## Safe Database Queries

```python
# ✅ SAFE - ORM parameterization
user = User.objects.get(username='john')

# ✅ SAFE - Explicit parameterization
User.objects.raw('SELECT * FROM auth_user WHERE username = %s', [username])

# ❌ DANGEROUS - String formatting
User.objects.raw(f'SELECT * FROM auth_user WHERE username = {username}')
```

---

## Query Validation

```python
from django.core.exceptions import ValidationError

def get_user_by_id(user_id):
    try:
        user_id = int(user_id)  # Validate type
        return User.objects.get(id=user_id)
    except (ValueError, User.DoesNotExist):
        raise ValidationError('Invalid user ID')
```

---

# PART 5: PASSWORD SECURITY

## Password Hashing

```python
# ✅ Django handles hashing automatically
user = User.objects.create_user(
    username='john',
    password='securepass123'
)

# Password is hashed with PBKDF2
user.check_password('securepass123')  # True
```

---

## Password Requirements

```python
# settings.py
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        'OPTIONS': {
            'min_length': 12,
        }
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]
```

---

## Secure Password Storage

```python
# Never store passwords in plain text
user.password = 'new_password'  # ❌ WRONG

# Always use set_password()
user.set_password('new_password')  # ✅ CORRECT
user.save()
```

---

# PART 6: AUTHENTICATION SECURITY

## Secure Login

```python
from django.contrib.auth import authenticate, login
from django.views.decorators.http import require_http_methods

@require_http_methods(["POST"])
def secure_login(request):
    username = request.POST.get('username')
    password = request.POST.get('password')
    
    user = authenticate(username=username, password=password)
    
    if user is not None:
        login(request, user)
        return redirect('dashboard')
    else:
        # Log failed attempt (for monitoring)
        logger.warning(f'Failed login attempt: {username}')
        return render(request, 'login.html', {'error': 'Invalid credentials'})
```

---

## Session Security

```python
# settings.py
SESSION_COOKIE_SECURE = True        # HTTPS only
SESSION_COOKIE_HTTPONLY = True      # No JS access
SESSION_COOKIE_SAMESITE = 'Strict'  # CSRF protection
SESSION_COOKIE_AGE = 3600           # 1 hour
```

---

# PART 7: AUTHORIZATION & PERMISSIONS

## Check Permissions

```python
@login_required
@permission_required('myapp.can_publish_post')
def publish_post(request):
    # Only users with permission can access
    return render(request, 'publish.html')
```

---

## Object-Level Permissions

```python
from django.shortcuts import get_object_or_404

def edit_post(request, post_id):
    post = get_object_or_404(Post, id=post_id)
    
    # Verify user is owner
    if post.author != request.user:
        raise PermissionDenied
    
    # Allow edit
    return render(request, 'edit_post.html', {'post': post})
```

---

# PART 8: HTTPS & SSL/TLS

## Force HTTPS

```python
# settings.py
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000  # 1 year
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
```

---

## SSL Certificate

```bash
# Let's Encrypt
sudo certbot certonly --webroot -w /var/www/myproject -d example.com
```

---

# PART 9: SECURE HEADERS

## Security Headers

```python
# middleware.py
class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        response = self.get_response(request)
        
        # Prevent clickjacking
        response['X-Frame-Options'] = 'DENY'
        
        # Prevent MIME sniffing
        response['X-Content-Type-Options'] = 'nosniff'
        
        # Enable XSS protection
        response['X-XSS-Protection'] = '1; mode=block'
        
        # Referrer policy
        response['Referrer-Policy'] = 'strict-origin-when-cross-origin'
        
        # Permissions policy
        response['Permissions-Policy'] = 'geolocation=(), microphone=(), camera=()'
        
        return response
```

---

# PART 10: INPUT VALIDATION & SANITIZATION

## Validate Input

```python
from django.core.exceptions import ValidationError
from django.core.validators import URLValidator, validate_email

def validate_user_input(email, website):
    # Validate email
    try:
        validate_email(email)
    except ValidationError:
        raise ValidationError('Invalid email')
    
    # Validate URL
    try:
        URLValidator()(website)
    except ValidationError:
        raise ValidationError('Invalid URL')
```

---

## Sanitize Output

```python
from django.utils.html import escape
from markupsafe import escape

# In views
user_input = escape(user_input)

# In templates - auto escaped by default
{{ user_input }}
```

---

# PART 11: FILE UPLOAD SECURITY

## Secure File Upload

```python
from django.core.validators import FileExtensionValidator
import os

class SecureFileUpload:
    ALLOWED_EXTENSIONS = {'pdf', 'doc', 'docx', 'txt'}
    MAX_FILE_SIZE = 5 * 1024 * 1024  # 5MB
    
    @staticmethod
    def validate_file(file):
        # Check extension
        ext = os.path.splitext(file.name)[1][1:].lower()
        if ext not in SecureFileUpload.ALLOWED_EXTENSIONS:
            raise ValidationError('File type not allowed')
        
        # Check file size
        if file.size > SecureFileUpload.MAX_FILE_SIZE:
            raise ValidationError('File too large')
        
        # Check MIME type
        if file.content_type not in ['application/pdf', 'application/msword']:
            raise ValidationError('Invalid file type')

def upload_file(request):
    file = request.FILES['file']
    SecureFileUpload.validate_file(file)
    # Safe to process file
```

---

# PART 12: API SECURITY

## Token Authentication

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
}
```

---

## Rate Limiting

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/hour',
        'user': '1000/hour'
    }
}
```

---

# PART 13: CORS SECURITY

## CORS Configuration

```python
# settings.py
from corsheaders.defaults import default_headers

CORS_ALLOWED_ORIGINS = [
    'https://example.com',
    'https://www.example.com',
]

CORS_ALLOW_HEADERS = list(default_headers) + [
    'x-custom-header',
]

CORS_ALLOW_METHODS = [
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
]
```

---

# PART 14: RATE LIMITING & DDoS

## Rate Limiting Middleware

```python
from django.core.cache import cache

class RateLimitMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        ip = self.get_client_ip(request)
        cache_key = f'rate_limit_{ip}'
        
        request_count = cache.get(cache_key, 0)
        if request_count >= 100:  # 100 requests per hour
            return HttpResponse('Rate limit exceeded', status=429)
        
        cache.set(cache_key, request_count + 1, 3600)
        return self.get_response(request)
    
    def get_client_ip(self, request):
        x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
        if x_forwarded_for:
            ip = x_forwarded_for.split(',')[0]
        else:
            ip = request.META.get('REMOTE_ADDR')
        return ip
```

---

# PART 15: LOGGING & MONITORING

## Security Logging

```python
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'security_file': {
            'level': 'WARNING',
            'class': 'logging.FileHandler',
            'filename': '/var/log/django/security.log',
        },
    },
    'loggers': {
        'django.security': {
            'handlers': ['security_file'],
            'level': 'WARNING',
            'propagate': False,
        },
    },
}
```

---

## Monitor Suspicious Activity

```python
import logging

logger = logging.getLogger('django.security')

def detect_suspicious_login(username):
    # Check for brute force
    cache_key = f'login_attempts_{username}'
    attempts = cache.get(cache_key, 0)
    
    if attempts > 5:
        logger.warning(f'Possible brute force: {username}')
        # Block login or require 2FA
```

---

# PART 16: SECURITY CHECKLIST

## Production Deployment

```
Security Settings:
☐ DEBUG = False
☐ SECRET_KEY changed
☐ ALLOWED_HOSTS configured
☐ SECURE_SSL_REDIRECT = True
☐ SECURE_HSTS_SECONDS set
☐ SESSION_COOKIE_SECURE = True
☐ CSRF_COOKIE_SECURE = True

Database:
☐ Strong database passwords
☐ Database backups automated
☐ Connection encryption enabled

Files & Uploads:
☐ Validate file extensions
☐ Check file sizes
☐ Scan for malware
☐ Store outside webroot

API Security:
☐ Token authentication enabled
☐ Rate limiting configured
☐ Input validation
☐ Output escaping
```

---

# PART 17: INTERVIEW Q&A

**Q: What's CSRF protection?**
A: CSRF tokens prevent unauthorized form submissions from other websites.

**Q: How does Django prevent XSS?**
A: Auto-escapes template variables. Use |safe only for trusted content.

**Q: What's HTTPS?**
A: Encrypted communication. Essential for production.

---

# PART 18: QUICK REFERENCE

| Security | Configuration |
|----------|----------------|
| CSRF | {% csrf_token %} |
| XSS | {{ var }} auto-escaped |
| SQL Injection | Use ORM |
| HTTPS | SECURE_SSL_REDIRECT = True |
| Headers | Security Headers Middleware |

---

**Secure your Django applications against attacks!** 🔒
