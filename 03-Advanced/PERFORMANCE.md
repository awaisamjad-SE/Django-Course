# ⚡ DJANGO PERFORMANCE - COMPLETE TUTORIAL A-Z

**Master Query Optimization, Caching Strategies, and Database Indexing**

---

## 📖 TABLE OF CONTENTS

1. Performance Fundamentals
2. Database Query Optimization
3. Select & Prefetch Related
4. Database Indexing
5. Query Caching
6. View Caching
7. Template Caching
8. Redis Caching
9. Database Connection Pooling
10. Lazy Loading & Pagination
11. Asynchronous Tasks
12. Asset Optimization
13. Profiling & Monitoring
14. Performance Testing
15. Scaling Strategies
16. 30+ Practical Examples
17. Interview Q&A
18. Quick Reference

---

# PART 1: PERFORMANCE FUNDAMENTALS

## Performance Pyramid

```
         /\
        /UI\
       /Perf\
      /-------\
     /Database\
    /  Queries \
   /--------- ---\
  /    Caching    \
 /     Strategy    \
/------------------\
```

---

## Performance Metrics

```
TTFB: Time to First Byte (< 200ms)
FCP: First Contentful Paint (< 1.8s)
LCP: Largest Contentful Paint (< 2.5s)
CLS: Cumulative Layout Shift (< 0.1)
```

---

# PART 2: DATABASE QUERY OPTIMIZATION

## N+1 Query Problem

```python
# ❌ BAD: N+1 queries
products = Product.objects.all()
for product in products:
    print(product.category.name)  # Query per product!

# ✅ GOOD: Single query with JOIN
products = Product.objects.select_related('category')
for product in products:
    print(product.category.name)
```

---

## Query Analysis

```python
from django.db import connection
from django.test.utils import CaptureQueriesContext

with CaptureQueriesContext(connection) as context:
    products = list(Product.objects.all())

print(f"Queries: {len(context)}")
for query in context:
    print(query['sql'])
```

---

## Only Get What You Need

```python
# ❌ ALL fields
products = Product.objects.all()

# ✅ Only needed fields
products = Product.objects.values_list('id', 'name')

# ✅ Defer heavy fields
products = Product.objects.defer('description')
```

---

# PART 3: SELECT & PREFETCH RELATED

## Select Related (Foreign Keys)

```python
# ❌ Bad: 2 queries
author = book.author.name

# ✅ Good: 1 query with JOIN
books = Book.objects.select_related('author')
```

---

## Prefetch Related (Reverse FK, M2M)

```python
# ❌ Bad: 1 + N queries
authors = Author.objects.all()
for author in authors:
    books = author.book_set.all()  # N queries

# ✅ Good: 2 queries total
authors = Author.objects.prefetch_related('book_set')
for author in authors:
    books = author.book_set.all()  # No extra query
```

---

## Prefetch with Filtering

```python
from django.db.models import Prefetch

# Complex prefetch
authors = Author.objects.prefetch_related(
    Prefetch(
        'book_set',
        queryset=Book.objects.filter(published=True)
    )
)
```

---

# PART 4: DATABASE INDEXING

## Create Indexes

```python
class Product(models.Model):
    name = models.CharField(max_length=100, db_index=True)
    slug = models.SlugField(unique=True)  # Auto-indexed
    category = models.ForeignKey(Category, on_delete=models.CASCADE, db_index=True)
    created_at = models.DateTimeField(auto_now_add=True, db_index=True)
    
    class Meta:
        indexes = [
            models.Index(fields=['name', 'category']),
            models.Index(fields=['-created_at']),
        ]
```

---

## Multi-Column Indexes

```python
class Meta:
    indexes = [
        models.Index(fields=['status', 'created_at']),
        models.Index(fields=['user', 'is_active']),
    ]
```

---

## Index Analysis

```sql
-- PostgreSQL
EXPLAIN ANALYZE SELECT * FROM products WHERE name LIKE 'Samsung%';

-- MySQL
EXPLAIN SELECT * FROM products WHERE name LIKE 'Samsung%';
```

---

# PART 5: QUERY CACHING

## QuerySet Caching

```python
# ❌ Multiple evaluations
for product in products:  # Evaluates
    print(product.name)

for product in products:  # Evaluates again!
    print(product.price)

# ✅ Cache result
products_list = list(products)  # One evaluation
for product in products_list:
    print(product.name)
for product in products_list:
    print(product.price)
```

---

# PART 6: VIEW CACHING

## Cache Decorator

```python
from django.views.decorators.cache import cache_page
from django.utils.decorators import method_decorator

@cache_page(60 * 15)  # 15 minutes
def product_list(request):
    products = Product.objects.all()
    return render(request, 'products.html', {'products': products})

# Class-based view
@method_decorator(cache_page(60 * 15), name='dispatch')
class ProductListView(ListView):
    model = Product
```

---

## Cache with Key

```python
from django.views.decorators.cache import cache_page
from django.utils.cache import get_cache_key

@cache_page(60 * 15)
def product_view(request, product_id):
    product = Product.objects.get(id=product_id)
    return render(request, 'product.html', {'product': product})
```

---

# PART 7: TEMPLATE CACHING

## Fragment Caching

```django
{% load cache %}

{# Cache for 600 seconds (10 minutes) #}
{% cache 600 product_detail product.id %}
    <div class="product">
        <h2>{{ product.name }}</h2>
        <p>{{ product.description }}</p>
        <p>Price: {{ product.price }}</p>
    </div>
{% endcache %}
```

---

## Cache Multiple Keys

```django
{% cache 600 product_detail product.id user.id %}
    {# Different cache for each user #}
    <p>{{ product.name }} for {{ user.username }}</p>
{% endcache %}
```

---

# PART 8: REDIS CACHING

## Redis Setup

```python
# settings.py
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

## Cache Operations

```python
from django.core.cache import cache

# Set
cache.set('product_1', product_data, 3600)

# Get
data = cache.get('product_1')

# Delete
cache.delete('product_1')

# Clear all
cache.clear()

# Increment
cache.incr('page_views', 1)

# Set default
cache.get_or_set('key', lambda: expensive_computation(), 3600)
```

---

## Cache Invalidation

```python
from django.db.models.signals import post_save

@receiver(post_save, sender=Product)
def invalidate_product_cache(sender, instance, **kwargs):
    cache.delete(f'product_{instance.id}')
    cache.delete('all_products')
```

---

# PART 9: DATABASE CONNECTION POOLING

## Connection Pooling (PostgreSQL)

```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydb',
        'CONN_MAX_AGE': 600,  # Connection lifetime
        'OPTIONS': {
            'connect_timeout': 10,
        }
    }
}
```

---

## With Connection Pooling (pgBouncer)

```
pgBouncer acts as connection pool proxy
Reduces connection overhead
Essential for high-concurrency apps
```

---

# PART 10: LAZY LOADING & PAGINATION

## Pagination

```python
from django.core.paginator import Paginator

def product_list(request):
    products = Product.objects.all()
    paginator = Paginator(products, 20)  # 20 per page
    page_num = request.GET.get('page', 1)
    page_obj = paginator.get_page(page_num)
    
    return render(request, 'products.html', {'page_obj': page_obj})
```

---

## Template Pagination

```django
{% if page_obj.has_previous %}
    <a href="?page=1">First</a>
    <a href="?page={{ page_obj.previous_page_number }}">Previous</a>
{% endif %}

Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}

{% if page_obj.has_next %}
    <a href="?page={{ page_obj.next_page_number }}">Next</a>
    <a href="?page={{ page_obj.paginator.num_pages }}">Last</a>
{% endif %}
```

---

# PART 11: ASYNCHRONOUS TASKS

## Celery for Background Jobs

```python
from celery import shared_task

@shared_task
def send_email_to_users(user_ids):
    users = User.objects.filter(id__in=user_ids)
    for user in users:
        send_email(user)

# Call asynchronously
send_email_to_users.delay([1, 2, 3])
```

---

# PART 12: ASSET OPTIMIZATION

## Compress Static Files

```python
# settings.py
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

## Image Optimization

```python
from PIL import Image

def optimize_image(image_path):
    img = Image.open(image_path)
    img.thumbnail((1920, 1080))
    img.save(image_path, quality=85, optimize=True)
```

---

# PART 13: PROFILING & MONITORING

## Django Debug Toolbar

```python
# settings.py
if DEBUG:
    INSTALLED_APPS += ['debug_toolbar']
    MIDDLEWARE += ['debug_toolbar.middleware.DebugToolbarMiddleware']
    INTERNAL_IPS = ['127.0.0.1']
```

---

## Query Profiling

```python
from django.db import connection
from django.conf import settings

if settings.DEBUG:
    print(f'Total queries: {len(connection.queries)}')
    for query in connection.queries:
        print(f"Time: {query['time']} - {query['sql']}")
```

---

# PART 14: PERFORMANCE TESTING

## Load Testing with Locust

```python
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 3)
    
    @task
    def index(self):
        self.client.get('/')
    
    @task
    def products(self):
        self.client.get('/products/')
```

---

# PART 15: SCALING STRATEGIES

## Horizontal Scaling

```
- Multiple application servers
- Load balancer (Nginx, HAProxy)
- Database replication
- Read replicas
- Redis cluster
- CDN for static files
```

---

## Vertical Scaling

```
- Increase CPU/RAM
- Optimize queries
- Enable caching
- Compress assets
- Use CDN
```

---

# PART 16: 30+ PRACTICAL EXAMPLES

## Example: Optimized Product List

```python
# views.py
from django.core.paginator import Paginator
from django.views.decorators.cache import cache_page

@cache_page(60 * 5)
def optimized_product_list(request):
    # Select only needed fields
    products = Product.objects.select_related('category').values(
        'id', 'name', 'price', 'category__name'
    )
    
    # Paginate
    paginator = Paginator(products, 20)
    page_obj = paginator.get_page(request.GET.get('page'))
    
    return render(request, 'products.html', {'page_obj': page_obj})
```

---

# PART 17: INTERVIEW Q&A

**Q: What's the N+1 query problem?**
A: When you fetch parent objects and then query for related objects repeatedly instead of using select_related/prefetch_related.

**Q: How do you cache views?**
A: Use @cache_page decorator or cache_view middleware.

**Q: When should you use select_related vs prefetch_related?**
A: select_related for ForeignKey/OneToOne. prefetch_related for reverse FK/M2M.

---

# PART 18: QUICK REFERENCE

| Optimization | Benefit |
|--------------|---------|
| select_related | Reduce queries (JOINs) |
| prefetch_related | Reduce queries (separate) |
| db_index | Speed up queries |
| @cache_page | Cache views |
| Fragment cache | Cache template parts |
| Redis | In-memory caching |

---

**Optimize Django applications for lightning-fast performance!** ⚡
