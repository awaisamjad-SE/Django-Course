# 📁 DJANGO STATIC FILES - COMPLETE TUTORIAL A-Z

**Master Static Files, Media Files, Collectstatic, and Serving Files**

---

## 📖 TABLE OF CONTENTS

1. Static Files Fundamentals
2. File Organization
3. Static Files in Templates
4. Django Collectstatic Command
5. Static Files Settings
6. Media Files Management
7. File Upload Handling
8. Serving Static Files
9. CDN Integration
10. Compression & Optimization
11. Caching Static Files
12. Security Best Practices
13. Performance Optimization
14. Testing Static Files
15. 30+ Practical Examples
16. Interview Q&A
17. Quick Reference

---

# PART 1: STATIC FILES FUNDAMENTALS

## What are Static Files?

Static files are CSS, JavaScript, images, and other assets that don't change per request.

### Types

```
Static: CSS, JS, images, fonts (public, cacheable)
Media: User uploads, dynamic content
```

---

## File Structure

```
myproject/
├── static/                    # Project-level static
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── script.js
│   └── img/
│       └── logo.png
└── myapp/
    └── static/               # App-level static
        └── myapp/
            ├── css/
            └── js/
```

---

# PART 2: FILE ORGANIZATION

## Best Structure

```
static/
├── css/
│   ├── style.css          # Main stylesheet
│   ├── bootstrap.min.css   # Third-party
│   └── responsive.css      # Responsive design
├── js/
│   ├── main.js            # Main script
│   ├── jquery.min.js      # Third-party
│   └── utils.js           # Utilities
├── images/
│   ├── logo.png
│   ├── favicon.ico
│   └── icons/
├── fonts/
│   ├── roboto.ttf
│   └── opensans.ttf
└── vendor/                # Third-party libraries
    ├── bootstrap/
    └── fontawesome/
```

---

## App-Level Organization

```
myapp/
└── static/
    └── myapp/             # Namespacing
        ├── css/
        │   └── myapp.css
        ├── js/
        │   └── myapp.js
        └── img/
            └── icon.png
```

---

# PART 3: STATIC FILES IN TEMPLATES

## Loading Static Files

```django
{% load static %}

<link rel="stylesheet" href="{% static 'css/style.css' %}">
<script src="{% static 'js/script.js' %}"></script>
<img src="{% static 'images/logo.png' %}" alt="Logo">
```

---

## Using STATIC_URL

```django
<link rel="stylesheet" href="{{ STATIC_URL }}css/style.css">

{# Or with static tag #}
{% load static %}
<link rel="stylesheet" href="{% static 'css/style.css' %}">
```

---

## Inline Styles & Scripts

```django
{% load static %}

<style>
    body { background-image: url('{% static "images/bg.png" %}'); }
</style>

<script>
    const logo = '{% static "images/logo.png" %}';
</script>
```

---

# PART 4: DJANGO COLLECTSTATIC COMMAND

## Collectstatic Command

```bash
# Collect all static files
python manage.py collectstatic

# Collect with verbosity
python manage.py collectstatic --verbosity 2

# Collect without interactive prompt
python manage.py collectstatic --noinput

# Clear before collecting
python manage.py collectstatic --clear

# Dry run (don't actually collect)
python manage.py collectstatic --dry-run
```

---

## Output

```
staticfiles/
├── css/
│   └── style.css
├── js/
│   └── script.js
└── images/
    └── logo.png
```

---

# PART 5: STATIC FILES SETTINGS

## Configure Settings

```python
# settings.py
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

# Static files URL
STATIC_URL = '/static/'

# Static files directory (for collectstatic)
STATIC_ROOT = BASE_DIR / 'staticfiles'

# Additional static files directories
STATICFILES_DIRS = [
    BASE_DIR / 'static',
]

# Finders for locating static files
STATICFILES_FINDERS = [
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
]
```

---

## Development vs Production

```python
# settings.py
if DEBUG:
    STATIC_URL = '/static/'
else:
    # Production with CDN
    STATIC_URL = 'https://cdn.example.com/static/'
    AWS_STORAGE_BUCKET_NAME = 'my-bucket'
```

---

# PART 6: MEDIA FILES MANAGEMENT

## Media Files Setup

```python
# settings.py
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

---

## Serve Media in Development

```python
# urls.py
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # Your URL patterns
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, 
                         document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, 
                         document_root=settings.STATIC_ROOT)
```

---

## Media File Organization

```
media/
├── user_uploads/
│   ├── 2024/
│   │   ├── 01/
│   │   │   └── user_pic.jpg
│   │   └── 02/
│   └── 2025/
├── profiles/
│   └── user_1/
│       └── avatar.png
└── documents/
    └── user_1/
        └── resume.pdf
```

---

# PART 7: FILE UPLOAD HANDLING

## File Upload Model

```python
from django.db import models

class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    avatar = models.ImageField(
        upload_to='profiles/%Y/%m/',
        default='default_avatar.png'
    )
    resume = models.FileField(
        upload_to='documents/%Y/%m/',
        blank=True
    )
    
    def __str__(self):
        return f"{self.user.username}'s profile"
```

---

## File Upload Form

```python
from django import forms

class FileUploadForm(forms.Form):
    file = forms.FileField()
    
    def clean_file(self):
        file = self.cleaned_data['file']
        
        # Check file size
        if file.size > 5 * 1024 * 1024:  # 5MB
            raise forms.ValidationError('File too large')
        
        # Check file type
        if not file.name.endswith(('.pdf', '.doc', '.docx')):
            raise forms.ValidationError('Invalid file type')
        
        return file
```

---

## Handle Upload View

```python
def upload_profile_picture(request):
    if request.method == 'POST':
        form = FileUploadForm(request.POST, request.FILES)
        if form.is_valid():
            profile = request.user.profile
            profile.avatar = request.FILES['file']
            profile.save()
            return redirect('profile')
    else:
        form = FileUploadForm()
    
    return render(request, 'upload.html', {'form': form})
```

---

# PART 8: SERVING STATIC FILES

## Development Serving

```python
# urls.py
from django.views.static import serve
from django.conf import settings

urlpatterns = [
    # Auto-served by Django in DEBUG=True
]
```

---

## Production Serving (Whitenoise)

```python
# settings.py
MIDDLEWARE = [
    'whitenoise.middleware.WhiteNoiseMiddleware',
    'django.middleware.security.SecurityMiddleware',
    # ... other middleware
]

# Enable compression
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

## Nginx Configuration

```nginx
server {
    listen 80;
    server_name example.com;

    location /static/ {
        alias /var/www/myproject/staticfiles/;
        expires 30d;
    }

    location /media/ {
        alias /var/www/myproject/media/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

---

# PART 9: CDN INTEGRATION

## AWS S3 Setup

```python
# settings.py
if not DEBUG:
    AWS_ACCESS_KEY_ID = os.environ.get('AWS_ACCESS_KEY_ID')
    AWS_SECRET_ACCESS_KEY = os.environ.get('AWS_SECRET_ACCESS_KEY')
    AWS_STORAGE_BUCKET_NAME = 'my-bucket'
    AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'
    AWS_S3_OBJECT_PARAMETERS = {'CacheControl': 'max-age=86400'}
    
    # S3 static settings
    STATIC_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/static/'
    STATIC_ROOT = 'static/'
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    
    # S3 public media settings
    MEDIA_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/media/'
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
```

---

# PART 10: COMPRESSION & OPTIMIZATION

## CSS/JS Minification

```python
# settings.py
STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.ManifestStaticFilesStorage'

# With compression
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

## Image Optimization

```python
from PIL import Image
from django.core.files.base import ContentFile
import io

class OptimizedImageField(models.ImageField):
    def save(self, name, content, save=True):
        if content:
            # Optimize image
            image = Image.open(content)
            image.thumbnail((1920, 1080))
            
            output = io.BytesIO()
            image.save(output, format='JPEG', quality=85)
            output.seek(0)
            
            content = ContentFile(output.getvalue(), name)
        
        return super().save(name, content, save)
```

---

# PART 11: CACHING STATIC FILES

## Cache Headers

```python
# settings.py
STATIC_ROOT = BASE_DIR / 'staticfiles'

# Enable versioning
STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.ManifestStaticFilesStorage'
```

---

## Cache Control

```python
# Nginx
location /static/ {
    expires 30d;
    add_header Cache-Control "public, immutable";
}
```

---

# PART 12: SECURITY BEST PRACTICES

## Secure Static Delivery

```python
# settings.py
STATICFILES_STORAGE = 'django.contrib.staticfiles.storage.ManifestStaticFilesStorage'

# Disable serving in production (use web server instead)
if DEBUG:
    STATIC_URL = '/static/'
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
        response['Content-Security-Policy'] = "default-src 'self'; script-src 'self' cdn.example.com"
        return response
```

---

# PART 13: PERFORMANCE OPTIMIZATION

## Lazy Loading Images

```django
<img src="{% static 'images/placeholder.png' %}" 
     data-src="{% static 'images/real-image.png' %}" 
     loading="lazy" 
     alt="Description">
```

---

## Responsive Images

```django
<picture>
    <source media="(min-width: 1024px)" 
            srcset="{% static 'images/large.jpg' %}">
    <source media="(min-width: 768px)" 
            srcset="{% static 'images/medium.jpg' %}">
    <img src="{% static 'images/small.jpg' %}" alt="Description">
</picture>
```

---

# PART 14: TESTING STATIC FILES

## Test Static File Collection

```python
from django.test import TestCase
from django.core.management import call_command
from django.conf import settings

class StaticFilesTest(TestCase):
    def test_collectstatic(self):
        call_command('collectstatic', verbosity=0, interactive=False)
        
        static_file = settings.STATIC_ROOT / 'css' / 'style.css'
        self.assertTrue(static_file.exists())
```

---

# PART 15: 30+ PRACTICAL EXAMPLES

## Example 1: Product Images

```python
class Product(models.Model):
    name = models.CharField(max_length=200)
    image = models.ImageField(
        upload_to='products/%Y/%m/',
        null=True,
        blank=True
    )
    thumbnail = models.ImageField(
        upload_to='products/thumbnails/',
        null=True,
        blank=True
    )
```

---

# PART 16: INTERVIEW Q&A

**Q: What's the difference between static and media files?**
A: Static files are CSS/JS (shipped with app). Media files are user uploads.

**Q: Why use collectstatic?**
A: To gather all static files into one directory for serving via web server.

**Q: How do you serve static files in production?**
A: Use a web server (Nginx, Apache) or CDN instead of Django.

---

# PART 17: QUICK REFERENCE

| Setting | Purpose |
|---------|---------|
| `STATIC_URL` | URL prefix for static files |
| `STATIC_ROOT` | Directory for collected files |
| `MEDIA_URL` | URL prefix for media |
| `MEDIA_ROOT` | Directory for uploads |

---

**Master static file management and optimize your Django app!** 📁
