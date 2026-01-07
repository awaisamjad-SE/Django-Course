# ⚙️ DJANGO MIDDLEWARE - COMPLETE TUTORIAL A-Z

**Master Middleware Concepts, Custom Middleware, and Request/Response Cycle**

---

## 📖 TABLE OF CONTENTS

1. Middleware Fundamentals
2. Request/Response Cycle
3. Built-in Middleware
4. Middleware Order & Priority
5. Writing Custom Middleware
6. Middleware Patterns
7. Exception Handling in Middleware
8. Middleware Testing
9. Performance Optimization
10. Security with Middleware
11. Debugging & Logging
12. CORS Middleware
13. Rate Limiting Middleware
14. Custom Headers Middleware
15. 30+ Practical Examples
16. Interview Q&A
17. Quick Reference

---

# PART 1: MIDDLEWARE FUNDAMENTALS

## What is Middleware?

Middleware is a series of hooks/filters that process requests before they reach views and responses before they're sent to clients.

### Architecture

```
Request
   ↓
Middleware 1 process_request()
   ↓
Middleware 2 process_request()
   ↓
URL Routing
   ↓
View
   ↓
Middleware 2 process_response()
   ↓
Middleware 1 process_response()
   ↓
Response
```

---

## Middleware Methods

```python
class MyMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        # Before view
        response = self.get_response(request)
        # After view
        return response
```

---

# PART 2: REQUEST/RESPONSE CYCLE

## Request Object

```python
# Accessing request attributes
request.method          # GET, POST, etc.
request.path           # /products/5/
request.GET            # Query parameters
request.POST           # Form data
request.FILES          # Uploaded files
request.COOKIES        # Browser cookies
request.META           # HTTP headers
request.user           # Authenticated user
request.session        # Session data
request.body           # Raw request body
```

---

## Response Object

```python
from django.http import HttpResponse, JsonResponse

# Simple response
response = HttpResponse("Hello")
response.status_code = 200
response['X-Custom-Header'] = 'value'
response.set_cookie('name', 'value')

# JSON response
response = JsonResponse({'key': 'value'})
response.status_code = 201

# Response with headers
response = HttpResponse()
response['Content-Type'] = 'application/json'
```

---

# PART 3: BUILT-IN MIDDLEWARE

## Common Built-in Middleware

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',           # HTTPS
    'django.contrib.sessions.middleware.SessionMiddleware',    # Sessions
    'django.middleware.common.CommonMiddleware',               # Normalize URLs
    'django.middleware.csrf.CsrfViewMiddleware',              # CSRF protection
    'django.contrib.auth.middleware.AuthenticationMiddleware', # Auth
    'django.contrib.messages.middleware.MessageMiddleware',    # Messages
    'django.middleware.clickjacking.XFrameOptionsMiddleware', # Clickjacking
]
```

---

## SecurityMiddleware

```python
# settings.py
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_SSL_REDIRECT = True
SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_SECURITY_POLICY = {
    'default-src': ("'self'",),
}
```

---

## SessionMiddleware

```python
# Enables request.session
request.session['user_id'] = user.id
user_id = request.session.get('user_id')
```

---

## CsrfViewMiddleware

```python
# Provides CSRF protection
# Must use {% csrf_token %} in forms
```

---

# PART 4: MIDDLEWARE ORDER & PRIORITY

## Correct Middleware Order

```python
MIDDLEWARE = [
    # Security first
    'django.middleware.security.SecurityMiddleware',
    
    # Sessions & Auth
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    
    # CSRF protection
    'django.middleware.csrf.CsrfViewMiddleware',
    
    # Other middleware
    'django.middleware.common.CommonMiddleware',
    
    # Messages
    'django.contrib.messages.middleware.MessageMiddleware',
    
    # Custom middleware
    'myapp.middleware.MyCustomMiddleware',
]
```

---

## Why Order Matters

```
Early middleware: Has raw request
Late middleware: Request already processed by other middleware

process_request: Execute in order (top to bottom)
process_response: Execute in reverse order (bottom to top)
```

---

# PART 5: WRITING CUSTOM MIDDLEWARE

## Basic Custom Middleware

```python
# middleware.py
from django.utils.deprecation import MiddlewareMixin
from django.http import HttpResponse

class SimpleMiddleware(MiddlewareMixin):
    def process_request(self, request):
        # Execute before view
        print(f"Incoming request: {request.path}")
    
    def process_response(self, request, response):
        # Execute after view
        print(f"Outgoing response: {response.status_code}")
        return response
```

---

## Modern Middleware (Class-based)

```python
class LoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        # Before view
        print(f"Request: {request.method} {request.path}")
        
        response = self.get_response(request)
        
        # After view
        print(f"Response: {response.status_code}")
        
        return response
```

---

## Exception Handling in Middleware

```python
class ExceptionHandlingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        try:
            response = self.get_response(request)
        except Exception as e:
            print(f"Error: {e}")
            return HttpResponse(f"Error: {e}", status=500)
        
        return response
```

---

# PART 6: MIDDLEWARE PATTERNS

## Timing Middleware

```python
import time

class TimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        start_time = time.time()
        response = self.get_response(request)
        duration = time.time() - start_time
        
        response['X-Process-Time'] = str(duration)
        return response
```

---

## Request/Response Modification

```python
class ModifyResponseMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        # Modify request
        request.custom_attribute = 'custom_value'
        
        response = self.get_response(request)
        
        # Modify response
        response['X-Custom-Header'] = 'header_value'
        
        return response
```

---

## Conditional Middleware

```python
class ConditionalMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        if request.path.startswith('/api/'):
            # Special handling for API
            pass
        
        response = self.get_response(request)
        return response
```

---

# PART 7: EXCEPTION HANDLING IN MIDDLEWARE

## Process Exception

```python
from django.utils.deprecation import MiddlewareMixin
from django.http import JsonResponse

class ErrorHandlingMiddleware(MiddlewareMixin):
    def process_exception(self, request, exception):
        print(f"Exception caught: {exception}")
        
        return JsonResponse({
            'error': 'Internal server error',
            'message': str(exception)
        }, status=500)
```

---

## View Exception

```python
class ViewExceptionMiddleware(MiddlewareMixin):
    def process_view(self, request, view_func, view_args, view_kwargs):
        # Execute before view
        print(f"About to call view: {view_func.__name__}")
```

---

# PART 8: MIDDLEWARE TESTING

## Test Custom Middleware

```python
from django.test import TestCase, RequestFactory
from .middleware import MyMiddleware

class MiddlewareTest(TestCase):
    def setUp(self):
        self.factory = RequestFactory()
    
    def test_middleware_adds_header(self):
        def dummy_view(request):
            return HttpResponse('OK')
        
        middleware = MyMiddleware(dummy_view)
        request = self.factory.get('/')
        response = middleware(request)
        
        self.assertIn('X-Custom-Header', response)
```

---

# PART 9: PERFORMANCE OPTIMIZATION

## Caching in Middleware

```python
from django.views.decorators.cache import cache_page
from django.core.cache import cache

class CachingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        cache_key = f"page_{request.path}"
        
        response = cache.get(cache_key)
        if response is None:
            response = self.get_response(request)
            cache.set(cache_key, response, 3600)
        
        return response
```

---

## Query Optimization

```python
class QueryCountMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        from django.db import connection
        from django.test.utils import CaptureQueriesContext
        
        with CaptureQueriesContext(connection) as ctx:
            response = self.get_response(request)
        
        print(f"Queries executed: {len(ctx)}")
        return response
```

---

# PART 10: SECURITY WITH MIDDLEWARE

## CORS Middleware

```python
class CORSMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        response = self.get_response(request)
        
        response['Access-Control-Allow-Origin'] = '*'
        response['Access-Control-Allow-Methods'] = 'GET, POST, PUT, DELETE'
        response['Access-Control-Allow-Headers'] = 'Content-Type'
        
        return response
```

---

## Security Headers

```python
class SecurityHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        response = self.get_response(request)
        
        response['X-Content-Type-Options'] = 'nosniff'
        response['X-Frame-Options'] = 'DENY'
        response['X-XSS-Protection'] = '1; mode=block'
        response['Referrer-Policy'] = 'strict-origin-when-cross-origin'
        
        return response
```

---

# PART 11: DEBUGGING & LOGGING

## Logging Middleware

```python
import logging
from django.utils.deprecation import MiddlewareMixin

logger = logging.getLogger(__name__)

class LoggingMiddleware(MiddlewareMixin):
    def process_request(self, request):
        logger.info(f"Request: {request.method} {request.path}")
    
    def process_response(self, request, response):
        logger.info(f"Response: {response.status_code}")
        return response
```

---

## Debug Toolbar Middleware

```python
# settings.py
if DEBUG:
    MIDDLEWARE += ['debug_toolbar.middleware.DebugToolbarMiddleware']
    INTERNAL_IPS = ['127.0.0.1']
```

---

# PART 12: RATE LIMITING MIDDLEWARE

## Simple Rate Limiter

```python
from django.core.cache import cache
from django.http import HttpResponse

class RateLimitMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        ip = self.get_client_ip(request)
        cache_key = f"rate_limit_{ip}"
        
        request_count = cache.get(cache_key, 0)
        
        if request_count >= 100:  # 100 requests per hour
            return HttpResponse("Rate limit exceeded", status=429)
        
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

# PART 13: CUSTOM HEADERS MIDDLEWARE

## Add Custom Headers

```python
class CustomHeadersMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        response = self.get_response(request)
        
        response['X-App-Version'] = '1.0.0'
        response['X-Request-ID'] = request.META.get('HTTP_X_REQUEST_ID', '')
        response['X-Powered-By'] = 'Django'
        
        return response
```

---

# PART 14: ADVANCED MIDDLEWARE PATTERNS

## Streaming Response

```python
from django.http import StreamingHttpResponse

class StreamingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        response = self.get_response(request)
        
        if isinstance(response, StreamingHttpResponse):
            response.streaming_content = self.stream_content(response.streaming_content)
        
        return response
    
    def stream_content(self, content):
        for chunk in content:
            yield chunk
```

---

# PART 15: 30+ PRACTICAL EXAMPLES

## Example 1: User Activity Tracking

```python
import logging

class ActivityTrackingMiddleware:
    logger = logging.getLogger('user_activity')
    
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        if request.user.is_authenticated:
            self.logger.info(
                f"User {request.user.username} accessed {request.path}"
            )
        
        response = self.get_response(request)
        return response
```

---

## Example 2: Request Statistics

```python
class StatsMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        self.request_count = 0
    
    def __call__(self, request):
        self.request_count += 1
        
        response = self.get_response(request)
        response['X-Request-Number'] = str(self.request_count)
        
        return response
```

---

# PART 16: INTERVIEW Q&A

**Q: What's the difference between process_request and process_response?**
A: process_request runs before view, process_response runs after view (in reverse order).

**Q: Can middleware modify request?**
A: Yes, middleware can add attributes and modify request before it reaches the view.

**Q: How do you handle exceptions in middleware?**
A: Use process_exception or try/except in __call__ method.

---

# PART 17: QUICK REFERENCE

| Method | When |
|--------|------|
| `process_request()` | Before view |
| `process_response()` | After view |
| `process_exception()` | When exception occurs |
| `process_view()` | Just before view |
| `process_template_response()` | After view returns |

---

**Master middleware for complete request/response control!** ⚙️
