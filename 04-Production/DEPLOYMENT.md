# 🚀 DJANGO DEPLOYMENT - COMPLETE TUTORIAL A-Z

**Master Production Deployment, Servers (Gunicorn, uWSGI), and Docker**

---

## 📖 TABLE OF CONTENTS

1. Deployment Fundamentals
2. Production Settings
3. Web Servers (Nginx, Apache)
4. Application Servers (Gunicorn, uWSGI)
5. WSGI & ASGI
6. Process Managers (Systemd, Supervisor)
7. Database Setup
8. Static & Media Files
9. SSL/HTTPS Configuration
10. Docker & Containerization
11. Docker Compose
12. Load Balancing
13. Monitoring & Logging
14. Backup & Recovery
15. Scaling Strategies
16. CI/CD Pipeline
17. Interview Q&A
18. Quick Reference

---

# PART 1: DEPLOYMENT FUNDAMENTALS

## Deployment Architecture

```
User Browser
    ↓
Load Balancer (HAProxy)
    ↓
Reverse Proxy (Nginx)
    ↓
Application Servers (Gunicorn × 4)
    ↓
Django Application
    ↓
Database (PostgreSQL)
    ↓
Static Files (CDN/S3)
```

---

## Pre-Deployment Checklist

```
✓ DEBUG = False
✓ ALLOWED_HOSTS configured
✓ SECRET_KEY secured
✓ Database configured
✓ Email configured
✓ Static files collected
✓ Media files directory created
✓ SSL certificates
✓ Backups configured
✓ Monitoring set up
```

---

# PART 2: PRODUCTION SETTINGS

## Environment-Specific Settings

```python
# settings.py
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent

# Security
DEBUG = os.getenv('DEBUG', 'False') == 'True'
SECRET_KEY = os.getenv('SECRET_KEY')
ALLOWED_HOSTS = os.getenv('ALLOWED_HOSTS', 'localhost').split(',')

# Database
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

# Allowed origins for CORS
CORS_ALLOWED_ORIGINS = [
    'https://example.com',
    'https://www.example.com',
]

# Cache configuration
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': os.getenv('REDIS_URL', 'redis://127.0.0.1:6379/1'),
    }
}
```

---

## Settings File Structure

```
settings/
├── __init__.py
├── base.py          # Common settings
├── development.py   # Development overrides
├── production.py    # Production overrides
└── testing.py       # Testing overrides
```

---

# PART 3: WEB SERVERS

## Nginx Configuration

```nginx
upstream django_app {
    least_conn;
    server 127.0.0.1:8000;
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
    server 127.0.0.1:8003;
}

server {
    listen 80;
    server_name example.com www.example.com;
    
    # Redirect HTTP to HTTPS
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com www.example.com;
    
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    
    client_max_body_size 10M;
    
    # Static files
    location /static/ {
        alias /var/www/myproject/staticfiles/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # Media files
    location /media/ {
        alias /var/www/myproject/media/;
        expires 7d;
    }
    
    # Proxy to application
    location / {
        proxy_pass http://django_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
    }
}
```

---

# PART 4: APPLICATION SERVERS

## Gunicorn Setup

```bash
# Install
pip install gunicorn

# Run
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000

# Production run (4 workers)
gunicorn myproject.wsgi:application \
    --bind 127.0.0.1:8000 \
    --workers 4 \
    --worker-class sync \
    --max-requests 1000 \
    --timeout 30
```

---

## Gunicorn Configuration File

```python
# gunicorn_config.py
import multiprocessing

bind = '127.0.0.1:8000'
workers = multiprocessing.cpu_count() * 2 + 1
worker_class = 'sync'
max_requests = 1000
max_requests_jitter = 100
timeout = 30
graceful_timeout = 30
keepalive = 2
```

---

## uWSGI Setup

```bash
# Install
pip install uWSGI

# Run
uwsgi --http :8000 --wsgi-file myproject/wsgi.py --master --processes 4
```

---

## uWSGI Configuration

```ini
[uwsgi]
http = 127.0.0.1:8000
wsgi-file = /var/www/myproject/myproject/wsgi.py
master = true
processes = 4
threads = 2
max-requests = 1000
max-requests-jitter = 100
harakiri = 30
```

---

# PART 5: WSGI & ASGI

## WSGI Application

```python
# wsgi.py
import os
from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

application = get_wsgi_application()
```

---

## ASGI for Async

```python
# asgi.py
import os
from django.core.asgi import get_asgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')

application = get_asgi_application()
```

---

# PART 6: PROCESS MANAGERS

## Systemd Service

```ini
# /etc/systemd/system/gunicorn.service
[Unit]
Description=Gunicorn Daemon
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject

ExecStart=/var/www/myproject/venv/bin/gunicorn \
    --workers 4 \
    --bind 127.0.0.1:8000 \
    myproject.wsgi:application

Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

---

## Supervisor Configuration

```ini
# /etc/supervisor/conf.d/gunicorn.conf
[program:gunicorn]
directory=/var/www/myproject
command=/var/www/myproject/venv/bin/gunicorn \
    --workers 4 \
    --bind 127.0.0.1:8000 \
    myproject.wsgi:application

user=www-data
autostart=true
autorestart=true
redirect_stderr=true
stdout_logfile=/var/log/gunicorn.log
```

---

# PART 7: DATABASE SETUP

## PostgreSQL Production Setup

```bash
# Install PostgreSQL
sudo apt-get install postgresql postgresql-contrib

# Create database
sudo -u postgres createdb myproject_db

# Create user
sudo -u postgres createuser myproject_user

# Set password
sudo -u postgres psql
ALTER USER myproject_user WITH PASSWORD 'securepass123';
ALTER ROLE myproject_user SET client_encoding TO 'utf8';
ALTER ROLE myproject_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE myproject_user SET default_transaction_deferrable TO on;
GRANT ALL PRIVILEGES ON DATABASE myproject_db TO myproject_user;
```

---

## Database Backup

```bash
# Backup
pg_dump -U myproject_user myproject_db > backup.sql

# Restore
psql -U myproject_user myproject_db < backup.sql

# Automated backup with cron
0 2 * * * pg_dump -U myproject_user myproject_db | gzip > /backups/db_$(date +\%Y\%m\%d).sql.gz
```

---

# PART 8: STATIC & MEDIA FILES

## Collectstatic

```bash
# Collect static files
python manage.py collectstatic --noinput

# For production with CDN
# AWS S3, CloudFront, Azure Blob Storage
```

---

# PART 9: SSL/HTTPS CONFIGURATION

## Let's Encrypt Setup

```bash
# Install Certbot
sudo apt-get install certbot python3-certbot-nginx

# Get certificate
sudo certbot certonly --webroot -w /var/www/myproject -d example.com

# Auto-renewal with cron
0 3 1 * * /usr/bin/certbot renew --quiet
```

---

# PART 10: DOCKER & CONTAINERIZATION

## Dockerfile

```dockerfile
FROM python:3.9

WORKDIR /app

ENV PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN python manage.py collectstatic --noinput

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "myproject.wsgi:application"]
```

---

## Build and Run

```bash
# Build image
docker build -t myproject:1.0 .

# Run container
docker run -d \
    --name myproject \
    -p 8000:8000 \
    -e DEBUG=False \
    -e SECRET_KEY=your-secret-key \
    myproject:1.0
```

---

# PART 11: DOCKER COMPOSE

## docker-compose.yml

```yaml
version: '3.8'

services:
  db:
    image: postgres:13
    environment:
      POSTGRES_DB: myproject_db
      POSTGRES_USER: myproject_user
      POSTGRES_PASSWORD: securepass
    volumes:
      - postgres_data:/var/lib/postgresql/data

  web:
    build: .
    command: gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    environment:
      DEBUG: 'False'
      DATABASE_URL: postgresql://myproject_user:securepass@db:5432/myproject_db
    depends_on:
      - db

  nginx:
    image: nginx:latest
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./static:/static
      - ./media:/media
    depends_on:
      - web

volumes:
  postgres_data:
```

---

## Deploy with Docker Compose

```bash
docker-compose up -d
docker-compose down
docker-compose logs -f web
```

---

# PART 12: LOAD BALANCING

## HAProxy Configuration

```
global
    maxconn 4096

frontend web_front
    bind *:80
    default_backend web_back

backend web_back
    balance leastconn
    server web1 127.0.0.1:8000 check
    server web2 127.0.0.1:8001 check
    server web3 127.0.0.1:8002 check
    server web4 127.0.0.1:8003 check
```

---

# PART 13: MONITORING & LOGGING

## Logging Configuration

```python
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': '/var/log/django/error.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'INFO',
            'propagate': True,
        },
    },
}
```

---

# PART 14: BACKUP & RECOVERY

## Automated Backups

```bash
#!/bin/bash
# backup.sh
BACKUP_DIR="/backups"
DATE=$(date +%Y%m%d_%H%M%S)

# Backup database
pg_dump -U myproject_user myproject_db | gzip > $BACKUP_DIR/db_$DATE.sql.gz

# Backup media files
tar -czf $BACKUP_DIR/media_$DATE.tar.gz /var/www/myproject/media

# Delete old backups (keep 30 days)
find $BACKUP_DIR -type f -mtime +30 -delete
```

---

# PART 15: SCALING STRATEGIES

## Horizontal Scaling

```
- Run multiple Gunicorn workers
- Use load balancer (HAProxy, Nginx)
- Database replication
- Cache layer (Redis)
- CDN for static files
```

---

## Vertical Scaling

```
- Increase server CPU/RAM
- Optimize database queries
- Enable caching
- Optimize static files
```

---

# PART 16: CI/CD PIPELINE

## GitHub Actions Deploy

```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Deploy to server
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.DEPLOY_KEY }}" > ~/.ssh/deploy_key
        chmod 600 ~/.ssh/deploy_key
        ssh-keyscan -H ${{ secrets.SERVER_HOST }} >> ~/.ssh/known_hosts
        
        ssh -i ~/.ssh/deploy_key ${{ secrets.SERVER_USER }}@${{ secrets.SERVER_HOST }} \
          'cd /var/www/myproject && git pull origin main && python manage.py migrate'
```

---

# PART 17: INTERVIEW Q&A

**Q: What's the difference between Gunicorn and Nginx?**
A: Gunicorn runs Django. Nginx is a reverse proxy/web server.

**Q: Why use Docker for deployment?**
A: Consistency, isolation, easy scaling, and reproducible environments.

**Q: How do you handle database migrations in production?**
A: Run `python manage.py migrate` before restarting app servers.

---

# PART 18: QUICK REFERENCE

| Component | Purpose |
|-----------|---------|
| Nginx | Reverse proxy, static files |
| Gunicorn | Application server |
| PostgreSQL | Database |
| Redis | Caching |
| Docker | Containerization |
| Supervisor | Process management |

---

**Deploy Django applications to production with confidence!** 🚀
