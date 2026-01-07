# ⚙️ DJANGO SETTINGS - COMPLETE TUTORIAL A-Z

**Master Configuration Management, Environment Variables, and Settings Structure**

---

## 📖 TABLE OF CONTENTS

1. Settings Fundamentals
2. Settings File Structure
3. Environment Variables
4. Development vs Production
5. Database Configuration
6. Cache Configuration
7. Email Configuration
8. Static & Media Files
9. Security Settings
10. Middleware Configuration
11. Template Configuration
12. Logging Configuration
13. Third-Party App Settings
14. Custom Settings
15. Settings Validation
16. 40+ Practical Examples
17. Interview Q&A
18. Quick Reference

---

# PART 1: SETTINGS FUNDAMENTALS

## Settings Module

```python
# settings.py - Central configuration file
import os
from pathlib import Path

# Build paths
BASE_DIR = Path(__file__).resolve().parent.parent

# Debug mode
DEBUG = True

# Secret key
SECRET_KEY = 'django-insecure-key'

# Allowed hosts
ALLOWED_HOSTS = ['localhost', '127.0.0.1']
```

---

## Loading Settings

```python
# Django automatically loads settings
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

# Access settings in code
from django.conf import settings

DEBUG = settings.DEBUG
ALLOWED_HOSTS = settings.ALLOWED_HOSTS
```

---

# PART 2: SETTINGS FILE STRUCTURE

## Single Settings File

```
myproject/
├── manage.py
├── myproject/
│   ├── __init__.py
│   ├── settings.py          # All settings
│   ├── urls.py
│   └── wsgi.py
```

---

## Multiple Settings Files (Recommended)

```
myproject/
├── manage.py
├── myproject/
│   ├── settings/
│   │   ├── __init__.py
│   │   ├── base.py          # Common settings
│   │   ├── development.py   # Dev overrides
│   │   ├── production.py    # Prod overrides
│   │   └── testing.py       # Test overrides
│   ├── urls.py
│   └── wsgi.py
```

---

## Base Settings (Shared)

```python
# settings/base.py
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent.parent

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
]

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
            ],
        },
    },
]
```

---

## Development Settings

```python
# settings/development.py
from .base import *

DEBUG = True

ALLOWED_HOSTS = ['localhost', '127.0.0.1']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

INSTALLED_APPS += ['debug_toolbar']
MIDDLEWARE += ['debug_toolbar.middleware.DebugToolbarMiddleware']
INTERNAL_IPS = ['127.0.0.1']
```

---

## Production Settings

```python
# settings/production.py
from .base import *

DEBUG = False

ALLOWED_HOSTS = ['example.com', 'www.example.com']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME'),
        'USER': os.getenv('DB_USER'),
        'PASSWORD': os.getenv('DB_PASSWORD'),
        'HOST': os.getenv('DB_HOST'),
        'PORT': os.getenv('DB_PORT', 5432),
    }
}

# Security
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
CSRF_COOKIE_SECURE = True
SESSION_COOKIE_SECURE = True
```

---

## Testing Settings

```python
# settings/testing.py
from .base import *

DEBUG = False

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': ':memory:',  # In-memory database
    }
}

# Speed up tests
PASSWORD_HASHERS = [
    'django.contrib.auth.hashers.MD5PasswordHasher',
]
```

---

# PART 3: ENVIRONMENT VARIABLES

## Using .env Files

```bash
# .env
DEBUG=True
SECRET_KEY=your-secret-key-here
DATABASE_URL=postgresql://user:pass@localhost/db
REDIS_URL=redis://localhost:6379
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your-email@gmail.com
EMAIL_PASSWORD=your-password
```

---

## Load Environment Variables

```python
# settings.py
import os
from pathlib import Path
from dotenv import load_dotenv

# Load .env file
load_dotenv()

BASE_DIR = Path(__file__).resolve().parent.parent

DEBUG = os.getenv('DEBUG', 'False') == 'True'
SECRET_KEY = os.getenv('SECRET_KEY')
ALLOWED_HOSTS = os.getenv('ALLOWED_HOSTS', 'localhost').split(',')

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME'),
        'USER': os.getenv('DB_USER'),
        'PASSWORD': os.getenv('DB_PASSWORD'),
    }
}
```

---

## Install python-dotenv

```bash
pip install python-dotenv
```

---

# PART 4: DEVELOPMENT VS PRODUCTION

## Development Settings

```python
DEBUG = True
ALLOWED_HOSTS = ['*']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'db.sqlite3',
    }
}

INSTALLED_APPS += ['debug_toolbar']

STATIC_URL = '/static/'
STATIC_ROOT = 'staticfiles/'

EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

---

## Production Settings

```python
DEBUG = False
ALLOWED_HOSTS = ['example.com']

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME'),
        'USER': os.getenv('DB_USER'),
        'PASSWORD': os.getenv('DB_PASSWORD'),
    }
}

SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True

STATIC_URL = 'https://cdn.example.com/static/'

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
```

---

# PART 5: DATABASE CONFIGURATION

## SQLite (Development)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

---

## PostgreSQL (Production)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME', 'mydb'),
        'USER': os.getenv('DB_USER', 'postgres'),
        'PASSWORD': os.getenv('DB_PASSWORD'),
        'HOST': os.getenv('DB_HOST', 'localhost'),
        'PORT': os.getenv('DB_PORT', '5432'),
        'CONN_MAX_AGE': 600,
        'OPTIONS': {
            'connect_timeout': 10,
        }
    }
}
```

---

## MySQL

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydb',
        'USER': 'root',
        'PASSWORD': 'password',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

---

## Multiple Databases

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'main_db',
    },
    'secondary': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'secondary_db',
    }
}

# Usage
User.objects.using('secondary').all()
User.objects.using('default').create(...)
```

---

# PART 6: CACHE CONFIGURATION

## In-Memory Cache

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake',
    }
}
```

---

## Redis Cache

```python
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            'PARSER_KWARGS': {},
            'CONNECTION_POOL_KWARGS': {'max_connections': 50},
        }
    }
}
```

---

## Memcached

```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.PyMemcacheCache',
        'LOCATION': '127.0.0.1:11211',
    }
}
```

---

# PART 7: EMAIL CONFIGURATION

## Console Email (Development)

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

---

## SMTP Email (Production)

```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = os.getenv('EMAIL_USER')
EMAIL_HOST_PASSWORD = os.getenv('EMAIL_PASSWORD')
DEFAULT_FROM_EMAIL = 'noreply@example.com'
```

---

## SendGrid

```python
EMAIL_BACKEND = 'sendgrid_backend.SendgridBackend'
SENDGRID_API_KEY = os.getenv('SENDGRID_API_KEY')
```

---

# PART 8: STATIC & MEDIA FILES

## Static Files

```python
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'
STATICFILES_DIRS = [BASE_DIR / 'static']

# For production
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

## Media Files

```python
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

---

## AWS S3

```python
AWS_ACCESS_KEY_ID = os.getenv('AWS_ACCESS_KEY_ID')
AWS_SECRET_ACCESS_KEY = os.getenv('AWS_SECRET_ACCESS_KEY')
AWS_STORAGE_BUCKET_NAME = 'my-bucket'
AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'

DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3StaticStorage'
```

---

# PART 9: SECURITY SETTINGS

## Essential Security Settings

```python
# Secret key
SECRET_KEY = os.getenv('SECRET_KEY')

# Debug mode
DEBUG = False  # Always False in production

# Allowed hosts
ALLOWED_HOSTS = ['example.com', 'www.example.com']

# HTTPS
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

# Cookies
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_SECURE = True
CSRF_COOKIE_HTTPONLY = True

# Headers
SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_SECURITY_POLICY = {
    'default-src': ("'self'",),
}

X_FRAME_OPTIONS = 'DENY'
```

---

# PART 10: MIDDLEWARE CONFIGURATION

## Default Middleware

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

---

## Adding Custom Middleware

```python
MIDDLEWARE = [
    # ... existing middleware
    'myapp.middleware.CustomMiddleware',
]
```

---

# PART 11: TEMPLATE CONFIGURATION

## Template Backends

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
                'myapp.context_processors.site_config',
            ],
            'builtins': [
                'myapp.templatetags.custom_tags',
            ],
        },
    },
]
```

---

# PART 12: LOGGING CONFIGURATION

## Logging Setup

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {message}',
            'style': '{',
        },
    },
    
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'verbose',
        },
        'file': {
            'class': 'logging.FileHandler',
            'filename': '/var/log/django/app.log',
            'formatter': 'verbose',
        },
    },
    
    'loggers': {
        'django': {
            'handlers': ['console', 'file'],
            'level': 'INFO',
        },
    },
}
```

---

# PART 13: THIRD-PARTY APP SETTINGS

## Django REST Framework

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,
}
```

---

## CORS Settings

```python
CORS_ALLOWED_ORIGINS = [
    'https://example.com',
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

# PART 14: CUSTOM SETTINGS

## Define Custom Settings

```python
# settings.py
MY_SETTING = os.getenv('MY_SETTING', 'default_value')

PAGINATION_SIZE = 20
MAX_FILE_SIZE = 5 * 1024 * 1024  # 5MB

# Access in code
from django.conf import settings

size = settings.MY_SETTING
```

---

# PART 15: SETTINGS VALIDATION

## Validate on Startup

```python
# settings.py
import os
from django.core.exceptions import ImproperlyConfigured

# Check required settings
if not os.getenv('SECRET_KEY'):
    raise ImproperlyConfigured("SECRET_KEY not set in environment")

if not DEBUG and not os.getenv('DATABASE_URL'):
    raise ImproperlyConfigured("DATABASE_URL required in production")
```

---

# PART 16: 40+ PRACTICAL EXAMPLES

## Example: Complete Settings File

```python
# settings/base.py
import os
from pathlib import Path
from dotenv import load_dotenv

load_dotenv()

BASE_DIR = Path(__file__).resolve().parent.parent.parent

SECRET_KEY = os.getenv('SECRET_KEY')
DEBUG = os.getenv('DEBUG', 'False') == 'True'
ALLOWED_HOSTS = os.getenv('ALLOWED_HOSTS', 'localhost').split(',')

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'corsheaders',
    'myapp',
]

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME'),
        'USER': os.getenv('DB_USER'),
        'PASSWORD': os.getenv('DB_PASSWORD'),
        'HOST': os.getenv('DB_HOST'),
        'PORT': os.getenv('DB_PORT', 5432),
    }
}

CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': os.getenv('REDIS_URL', 'redis://127.0.0.1:6379/1'),
    }
}

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

# PART 17: INTERVIEW Q&A

**Q: Why use environment variables?**
A: Keep sensitive data out of source code and support different configs per environment.

**Q: What's the difference between DEBUG=True and False?**
A: DEBUG=True shows errors, serves static files. DEBUG=False hides errors for security.

**Q: How do you organize large settings files?**
A: Use settings directory with base, development, production, testing files.

---

# PART 18: QUICK REFERENCE

| Setting | Purpose |
|---------|---------|
| DEBUG | Development mode |
| SECRET_KEY | CSRF/session token |
| ALLOWED_HOSTS | Allowed domain |
| DATABASES | DB config |
| INSTALLED_APPS | Loaded apps |
| MIDDLEWARE | Request processors |
| STATIC_URL | Static files URL |
| MEDIA_URL | Media files URL |

---

**Master Django configuration and manage settings like a pro!** ⚙️
