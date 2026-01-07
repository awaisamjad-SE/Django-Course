# 📝 DJANGO EXCEPTIONS & LOGGING - COMPLETE TUTORIAL A-Z

**Master Error Handling, Custom Exceptions, and Logging**

---

## 📖 TABLE OF CONTENTS

1. Exception Handling Fundamentals
2. Built-in Exceptions
3. Custom Exceptions
4. Try/Except Patterns
5. Exception Views
6. Error Pages (403, 404, 500)
7. Logging Fundamentals
8. Logging Configuration
9. Logger Usage
10. Log Handlers
11. Log Formatters
12. Error Tracking (Sentry)
13. Debug Toolbar
14. Performance Monitoring
15. Best Practices
16. 30+ Practical Examples
17. Interview Q&A
18. Quick Reference

---

# PART 1: EXCEPTION HANDLING FUNDAMENTALS

## Django Exception Hierarchy

```
Exception
├── Http404
├── PermissionDenied
├── ValidationError
├── ObjectDoesNotExist
│   └── DoesNotExist
└── MultipleObjectsReturned
```

---

## Basic Try/Except

```python
try:
    product = Product.objects.get(id=1)
except Product.DoesNotExist:
    # Handle not found
    raise Http404("Product not found")
except Exception as e:
    # Handle other errors
    logger.error(f"Unexpected error: {e}")
```

---

# PART 2: BUILT-IN EXCEPTIONS

## Common Django Exceptions

```python
from django.http import Http404
from django.core.exceptions import (
    PermissionDenied,
    ValidationError,
    ObjectDoesNotExist,
    MultipleObjectsReturned
)

# 404 Not Found
raise Http404("Page not found")

# 403 Forbidden
raise PermissionDenied("You don't have permission")

# Validation error
raise ValidationError("Invalid input")

# Database errors
try:
    obj = Model.objects.get(id=1)
except Model.DoesNotExist:
    pass
except Model.MultipleObjectsReturned:
    pass
```

---

# PART 3: CUSTOM EXCEPTIONS

## Define Custom Exceptions

```python
# exceptions.py
class APIError(Exception):
    """Base API exception"""
    def __init__(self, message, status_code=400):
        self.message = message
        self.status_code = status_code
        super().__init__(self.message)

class InvalidInputError(APIError):
    """Raised when input validation fails"""
    pass

class ResourceNotFoundError(APIError):
    """Raised when resource not found"""
    def __init__(self, resource_name):
        super().__init__(f"{resource_name} not found", 404)

class InsufficientPermissionError(APIError):
    """Raised when user lacks permission"""
    def __init__(self, action):
        super().__init__(f"Permission denied for {action}", 403)
```

---

## Raise Custom Exceptions

```python
def get_product(product_id):
    try:
        return Product.objects.get(id=product_id)
    except Product.DoesNotExist:
        raise ResourceNotFoundError("Product")

def edit_product(request, product_id):
    product = get_product(product_id)
    
    if product.author != request.user:
        raise InsufficientPermissionError("edit this product")
    
    # Continue with edit
```

---

# PART 4: TRY/EXCEPT PATTERNS

## Specific Exception Handling

```python
# ✅ GOOD: Catch specific exception
try:
    product = Product.objects.get(id=1)
except Product.DoesNotExist:
    # Handle specifically
    return redirect('product_not_found')

# ❌ BAD: Catch all exceptions
try:
    product = Product.objects.get(id=1)
except Exception:
    # Too broad, masks bugs
    pass
```

---

## Multiple Exception Handling

```python
try:
    product = Product.objects.get(id=product_id)
except (Product.DoesNotExist, ValueError):
    return Http404()
except ValidationError as e:
    logger.warning(f"Validation error: {e}")
    return render(request, 'error.html', {'error': str(e)})
finally:
    # Always executes
    logger.info("Operation completed")
```

---

## Context Manager Pattern

```python
from contextlib import contextmanager

@contextmanager
def handle_db_error():
    try:
        yield
    except DatabaseError as e:
        logger.error(f"Database error: {e}")
        # Recovery logic
    finally:
        # Cleanup
        pass

# Usage
with handle_db_error():
    product = Product.objects.get(id=1)
```

---

# PART 5: EXCEPTION VIEWS

## Handle Exceptions in Views

```python
from django.shortcuts import render
from django.http import Http404
from .exceptions import ResourceNotFoundError

def product_detail(request, product_id):
    try:
        product = Product.objects.get(id=product_id)
    except Product.DoesNotExist:
        raise Http404("Product not found")
    
    return render(request, 'product.html', {'product': product})

def api_endpoint(request):
    try:
        product_id = int(request.GET.get('id'))
        product = Product.objects.get(id=product_id)
    except ValueError:
        return JsonResponse({'error': 'Invalid ID'}, status=400)
    except ResourceNotFoundError as e:
        return JsonResponse({'error': e.message}, status=e.status_code)
```

---

# PART 6: ERROR PAGES

## Custom 404 Page

```python
# urls.py
handler404 = 'myapp.views.custom_404'
handler500 = 'myapp.views.custom_500'

# views.py
def custom_404(request, exception=None):
    return render(request, '404.html', status=404)

def custom_500(request):
    return render(request, '500.html', status=500)
```

---

## Error Templates

```django
<!-- 404.html -->
<h1>Page Not Found</h1>
<p>Sorry, the page you're looking for doesn't exist.</p>
<a href="/">Go Home</a>

<!-- 500.html -->
<h1>Server Error</h1>
<p>Something went wrong on our end. Please try again.</p>
<p>Error ID: {{ error_id }}</p>
```

---

# PART 7: LOGGING FUNDAMENTALS

## Logger Hierarchy

```
root logger
├── django
│   ├── django.db
│   ├── django.security
│   └── django.request
├── myapp
│   ├── myapp.models
│   └── myapp.views
└── celery
```

---

## Logger Usage

```python
import logging

logger = logging.getLogger(__name__)

# Log levels
logger.debug("Detailed debugging info")
logger.info("General informational messages")
logger.warning("Warning messages (default)")
logger.error("Error messages")
logger.critical("Critical errors")
```

---

# PART 8: LOGGING CONFIGURATION

## Settings Configuration

```python
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {process:d} {thread:d} {message}',
            'style': '{'
        },
        'simple': {
            'format': '{levelname} {message}',
            'style': '{'
        },
    },
    
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'simple'
        },
        'file': {
            'class': 'logging.FileHandler',
            'filename': '/var/log/django/app.log',
            'formatter': 'verbose'
        },
    },
    
    'loggers': {
        'django': {
            'handlers': ['console', 'file'],
            'level': 'INFO',
        },
        'myapp': {
            'handlers': ['console', 'file'],
            'level': 'DEBUG',
        },
    },
}
```

---

# PART 9: LOGGER USAGE

## Application Logging

```python
import logging

logger = logging.getLogger(__name__)

def create_product(request):
    try:
        product = Product.objects.create(
            name=request.POST['name'],
            price=request.POST['price']
        )
        logger.info(f"Product created: {product.id}")
        return redirect('product_detail', pk=product.id)
    
    except ValueError as e:
        logger.error(f"Invalid product data: {e}")
        return render(request, 'create_product.html', {'error': str(e)})
    
    except Exception as e:
        logger.critical(f"Unexpected error: {e}", exc_info=True)
        return render(request, 'error.html', status=500)
```

---

## Context Information

```python
import logging

logger = logging.getLogger(__name__)

def log_with_context(request, action):
    logger.info(
        f"Action: {action}",
        extra={
            'user': request.user.username,
            'ip': request.META.get('REMOTE_ADDR'),
            'path': request.path,
        }
    )
```

---

# PART 10: LOG HANDLERS

## File Handler

```python
handler = logging.FileHandler('/var/log/django/app.log')
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)
```

---

## Rotating File Handler

```python
from logging.handlers import RotatingFileHandler

handler = RotatingFileHandler(
    '/var/log/django/app.log',
    maxBytes=10485760,  # 10MB
    backupCount=5       # Keep 5 files
)
logger.addHandler(handler)
```

---

## Email Handler (Errors)

```python
from logging.handlers import SMTPHandler

handler = SMTPHandler(
    mailhost='smtp.example.com',
    fromaddr='app@example.com',
    toaddrs=['admin@example.com'],
    subject='Django Error'
)
handler.setLevel(logging.ERROR)
logger.addHandler(handler)
```

---

# PART 11: LOG FORMATTERS

## Custom Formatter

```python
import logging

class CustomFormatter(logging.Formatter):
    FORMATS = {
        logging.DEBUG: "🐛 DEBUG: %(message)s",
        logging.INFO: "ℹ️ INFO: %(message)s",
        logging.WARNING: "⚠️ WARNING: %(message)s",
        logging.ERROR: "❌ ERROR: %(message)s",
        logging.CRITICAL: "🔥 CRITICAL: %(message)s",
    }
    
    def format(self, record):
        log_fmt = self.FORMATS.get(record.levelno)
        formatter = logging.Formatter(log_fmt)
        return formatter.format(record)
```

---

# PART 12: ERROR TRACKING (SENTRY)

## Sentry Integration

```python
# settings.py
import sentry_sdk
from sentry_sdk.integrations.django import DjangoIntegration

sentry_sdk.init(
    dsn="https://key@sentry.io/project",
    integrations=[DjangoIntegration()],
    traces_sample_rate=1.0,
    send_default_pii=False
)
```

---

## Track Errors

```python
def problematic_view(request):
    try:
        # Code that might fail
        pass
    except Exception as e:
        # Automatically tracked by Sentry
        raise
```

---

# PART 13: DEBUG TOOLBAR

## Install & Configure

```python
# settings.py
if DEBUG:
    INSTALLED_APPS += ['debug_toolbar']
    MIDDLEWARE += ['debug_toolbar.middleware.DebugToolbarMiddleware']
    INTERNAL_IPS = ['127.0.0.1']
```

---

## Features

```
- SQL queries
- Templates used
- Cache usage
- Request/response headers
- Email sent
- Performance metrics
```

---

# PART 14: BEST PRACTICES

## Logging Best Practices

```python
# ✅ GOOD
logger.error("User %s failed to login", username)
logger.error("Database error", exc_info=True)

# ❌ BAD
logger.error(f"User {username} failed to login")
logger.error(str(exception))
```

---

## Exception Handling Best Practices

```python
# ✅ GOOD: Specific, recoverable
try:
    product = Product.objects.get(id=product_id)
except Product.DoesNotExist:
    return Http404()

# ❌ BAD: Too broad, masks bugs
try:
    product = Product.objects.get(id=product_id)
except Exception:
    return Http500()
```

---

# PART 15: 30+ PRACTICAL EXAMPLES

## Example: Complete Error Handling

```python
import logging

logger = logging.getLogger(__name__)

def get_product_with_logging(product_id):
    try:
        logger.debug(f"Fetching product {product_id}")
        product = Product.objects.get(id=product_id)
        logger.info(f"Product {product_id} fetched successfully")
        return product
    
    except Product.DoesNotExist:
        logger.warning(f"Product {product_id} not found")
        raise Http404("Product not found")
    
    except ValueError:
        logger.error(f"Invalid product ID: {product_id}")
        raise ValidationError("Invalid product ID")
    
    except Exception as e:
        logger.critical(f"Unexpected error: {e}", exc_info=True)
        raise
```

---

# PART 16: INTERVIEW Q&A

**Q: What's the difference between logging and print()?**
A: Logging is configurable (level, handlers), print() is not and goes to stdout.

**Q: How do you log exceptions?**
A: Use `logger.error("msg", exc_info=True)` to include traceback.

**Q: Should you catch all exceptions?**
A: No, catch specific exceptions to avoid masking bugs.

---

# PART 17: QUICK REFERENCE

| Level | Use Case |
|-------|----------|
| DEBUG | Detailed info |
| INFO | General info |
| WARNING | Warnings (default) |
| ERROR | Error occurred |
| CRITICAL | Critical error |

---

**Master error handling and logging for reliable applications!** 📝
