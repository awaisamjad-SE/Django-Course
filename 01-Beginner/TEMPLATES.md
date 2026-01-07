# 📄 DJANGO TEMPLATES - COMPLETE TUTORIAL A-Z

**Master Template Language, Rendering, Inheritance, and Context Processors**

---

## 📖 TABLE OF CONTENTS

1. Template Fundamentals
2. Template Syntax & Tags
3. Filters & Formatting
4. Template Inheritance
5. Template Inclusion & Reusability
6. Context & Variables
7. Context Processors
8. Custom Template Tags & Filters
9. Template Loading & Configuration
10. Static Files in Templates
11. Form Rendering in Templates
12. Template Security & Best Practices
13. Performance Optimization
14. Template Testing
15. Common Patterns
16. 50+ Practical Examples
17. Interview Q&A
18. Quick Reference

---

# PART 1: TEMPLATE FUNDAMENTALS

## What are Templates?

Templates are HTML files with Django-specific syntax that dynamically generate web pages. They separate presentation from logic.

### Architecture
```
View → Context Data → Template → Rendered HTML → Response
```

### Basic Template File Structure
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Django Site</title>
</head>
<body>
    <h1>Welcome, {{ user.name }}</h1>
    <p>{{ page_title }}</p>
</body>
</html>
```

---

## Template Rendering in Views

### Function-Based View
```python
from django.shortcuts import render
from .models import Product

def product_list(request):
    products = Product.objects.all()
    context = {
        'products': products,
        'page_title': 'Our Products'
    }
    return render(request, 'products/list.html', context)
```

### Class-Based View
```python
from django.views.generic import ListView
from .models import Product

class ProductListView(ListView):
    model = Product
    template_name = 'products/list.html'
    context_object_name = 'products'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['page_title'] = 'Our Products'
        return context
```

---

# PART 2: TEMPLATE SYNTAX & TAGS

## Variables

```django
{{ variable }}
{{ object.attribute }}
{{ dictionary.key }}
{{ list.0 }}
```

### Example
```django
<h1>{{ product.name }}</h1>
<p>Price: {{ product.price }}</p>
<p>First comment: {{ comments.0.text }}</p>
```

---

## Control Flow Tags

### if/elif/else
```django
{% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}!</p>
{% elif user.is_anonymous %}
    <p>Please <a href="/login/">login</a></p>
{% else %}
    <p>Unknown user</p>
{% endif %}
```

### for Loop
```django
<ul>
{% for product in products %}
    <li>
        {{ product.name }} - ${{ product.price }}
    </li>
{% empty %}
    <li>No products available</li>
{% endfor %}
</ul>
```

### for Loop Variables
```django
{% for item in items %}
    {{ forloop.counter }}      {# 1, 2, 3... #}
    {{ forloop.counter0 }}     {# 0, 1, 2... #}
    {{ forloop.revcounter }}   {# 3, 2, 1... #}
    {{ forloop.first }}        {# True if first #}
    {{ forloop.last }}         {# True if last #}
    {{ forloop.parentloop }}   {# Parent loop #}
{% endfor %}
```

---

## Block & Extends Tags

```django
{% block content %}
    Default content
{% endblock %}

{% extends "base.html" %}
```

---

# PART 3: FILTERS & FORMATTING

## String Filters

```django
{{ text|upper }}              {# Uppercase #}
{{ text|lower }}              {# Lowercase #}
{{ text|title }}              {# Title Case #}
{{ text|truncatewords:3 }}    {# Truncate at word #}
{{ text|truncatechars:20 }}   {# Truncate at char #}
{{ text|slugify }}            {# Convert to slug #}
{{ text|escape }}             {# Escape HTML #}
```

### Example
```django
<h1>{{ product_name|upper }}</h1>
<p>{{ description|truncatewords:20 }}</p>
<a href="/{{ name|slugify }}/">View Details</a>
```

---

## Date & Time Filters

```django
{{ date|date:"Y-m-d" }}           {# 2024-01-15 #}
{{ date|date:"D, M d, Y" }}       {# Mon, Jan 15, 2024 #}
{{ date|time:"H:i:s" }}           {# 14:30:45 #}
{{ date|naturalday }}             {# Today, Tomorrow, Yesterday #}
{{ date|timesince }}              {# 2 hours ago #}
{{ date|timeuntil }}              {# in 2 hours #}
```

### Example
```django
<p>Posted: {{ article.created_at|date:"M d, Y" }}</p>
<p>{{ article.created_at|timesince }} ago</p>
```

---

## Number Filters

```django
{{ value|add:"5" }}               {# Add 5 #}
{{ price|floatformat:2 }}         {# 2 decimal places #}
{{ items|length }}                {# Count items #}
{{ number|pluralize }}            {# item/items #}
```

### Example
```django
<p>Price: ${{ product.price|floatformat:2 }}</p>
<p>{{ comments|length }} comment{{ comments|pluralize }}</p>
```

---

## Custom Filters

```python
# filters.py
from django import template

register = template.Library()

@register.filter
def multiply(value, arg):
    return value * arg

@register.filter(name='uppercase_first')
def uppercase_first(value):
    return value[0].upper() + value[1:] if value else value
```

### In Template
```django
{% load your_filters %}
{{ price|multiply:2 }}
{{ text|uppercase_first }}
```

---

# PART 4: TEMPLATE INHERITANCE

## Base Template (base.html)

```html
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
    <link rel="stylesheet" href="style.css">
    {% block extra_css %}{% endblock %}
</head>
<body>
    <header>
        <h1>My Django Site</h1>
        <nav>
            {% block nav %}
                <a href="/">Home</a>
                <a href="/about/">About</a>
            {% endblock %}
        </nav>
    </header>

    <main>
        {% block content %}
            Default content
        {% endblock %}
    </main>

    <footer>
        {% block footer %}
            <p>&copy; 2024 My Site</p>
        {% endblock %}
    </footer>

    {% block extra_js %}{% endblock %}
</body>
</html>
```

---

## Child Template (product_detail.html)

```django
{% extends "base.html" %}

{% block title %}{{ product.name }} - My Site{% endblock %}

{% block extra_css %}
    <link rel="stylesheet" href="product.css">
{% endblock %}

{% block content %}
    <div class="product">
        <h2>{{ product.name }}</h2>
        <p class="price">Price: ${{ product.price|floatformat:2 }}</p>
        <p>{{ product.description }}</p>
        <button>Add to Cart</button>
    </div>
{% endblock %}
```

---

## Inheritance Tips

```django
{# Override and extend #}
{% block content %}
    {{ block.super }}
    <p>Additional content</p>
{% endblock %}

{# Multiple inheritance levels #}
{# base.html → layout.html → product_detail.html #}
```

---

# PART 5: TEMPLATE INCLUSION & REUSABILITY

## Include Tag

```django
{% include "header.html" %}
{% include "footer.html" with site_name="My Store" %}
{% include "product_card.html" with product=item %}
```

### Component Template (product_card.html)

```django
<div class="product-card">
    <h3>{{ product.name }}</h3>
    <p class="price">${{ product.price }}</p>
    {% if product.in_stock %}
        <button class="add-to-cart">Add to Cart</button>
    {% else %}
        <p class="out-of-stock">Out of Stock</p>
    {% endif %}
</div>
```

---

## Include with Loop

```django
{% for product in products %}
    {% include "product_card.html" with product=item %}
{% endfor %}
```

---

# PART 6: CONTEXT & VARIABLES

## Passing Context from View

```python
def product_view(request):
    context = {
        'product': Product.objects.get(id=1),
        'related_products': Product.objects.filter(category='electronics')[:5],
        'is_featured': True,
        'user_has_purchased': request.user.purchases.exists()
    }
    return render(request, 'product.html', context)
```

---

## Accessing Context in Template

```django
{# Simple variable #}
{{ product.name }}

{# Dictionary #}
{{ config.site_name }}

{# List/QuerySet #}
{{ products.0.name }}

{# Method call (with no arguments) #}
{{ user.get_full_name }}

{# Attribute access #}
<img src="{{ product.image.url }}" />
```

---

## Default Values

```django
{{ variable|default:"N/A" }}
{{ variable|default_if_none:"Value not set" }}
```

---

# PART 7: CONTEXT PROCESSORS

## Built-in Context Processors

```python
# settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

---

## Custom Context Processor

```python
# context_processors.py
from django.conf import settings

def site_config(request):
    return {
        'site_name': 'My Store',
        'support_email': 'support@example.com',
        'current_year': 2024,
    }

def featured_products(request):
    from .models import Product
    return {
        'featured': Product.objects.filter(featured=True)[:5]
    }
```

### Register in Settings

```python
# settings.py
TEMPLATES = [
    {
        'OPTIONS': {
            'context_processors': [
                'django.contrib.auth.context_processors.auth',
                'myapp.context_processors.site_config',
                'myapp.context_processors.featured_products',
            ],
        },
    },
]
```

### Use in Template

```django
<h1>{{ site_name }}</h1>
<p>Contact: {{ support_email }}</p>
<p>&copy; {{ current_year }}</p>

{% for product in featured %}
    <div class="featured">{{ product.name }}</div>
{% endfor %}
```

---

# PART 8: CUSTOM TEMPLATE TAGS & FILTERS

## Custom Tags (Simple)

```python
# templatetags/custom_tags.py
from django import template

register = template.Library()

@register.simple_tag
def multiply(a, b):
    return a * b

@register.simple_tag
def get_admin_url(obj):
    return f"/admin/{obj._meta.app_label}/{obj._meta.model_name}/{obj.pk}/change/"
```

### In Template

```django
{% load custom_tags %}
Total: {% multiply 5 10 %}  {# Output: Total: 50 #}
Admin URL: {% get_admin_url product %}
```

---

## Custom Tags (Inclusion Tags)

```python
# templatetags/product_tags.py
from django import template
from myapp.models import Product

register = template.Library()

@register.inclusion_tag('product_list_snippet.html')
def product_list(count=5):
    products = Product.objects.all()[:count]
    return {'products': products}
```

### Template (product_list_snippet.html)

```django
<ul class="products">
    {% for product in products %}
        <li>{{ product.name }} - ${{ product.price }}</li>
    {% endfor %}
</ul>
```

### Usage

```django
{% load product_tags %}
{% product_list 10 %}
```

---

# PART 9: TEMPLATE LOADING & CONFIGURATION

## Template Directories

```python
# settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            BASE_DIR / 'templates',  # Project-level
            BASE_DIR / 'common_templates',
        ],
        'APP_DIRS': True,  # Enable app-level templates
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.auth',
            ],
        },
    },
]
```

---

## Template Structure

```
myproject/
├── templates/                    # Project-level
│   ├── base.html
│   ├── home.html
│   └── includes/
│       ├── header.html
│       └── footer.html
└── myapp/
    └── templates/               # App-level
        └── myapp/
            ├── product_list.html
            └── product_detail.html
```

---

## Loading Templates

```python
from django.template.loader import render_to_string, get_template

# Option 1: render_to_string
html = render_to_string('product.html', {'product': product})

# Option 2: get_template + render
template = get_template('product.html')
html = template.render({'product': product})

# Option 3: render shortcut (in views)
return render(request, 'product.html', context)
```

---

# PART 10: STATIC FILES IN TEMPLATES

## Loading Static Files

```django
{% load static %}

<img src="{% static 'img/logo.png' %}" />
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<script src="{% static 'js/script.js' %}"></script>
```

---

## With STATIC_URL

```django
<img src="{{ STATIC_URL }}img/logo.png" />
```

---

# PART 11: FORM RENDERING IN TEMPLATES

## Rendering Forms

```django
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>
```

---

## Manual Field Rendering

```django
<form method="post">
    {% csrf_token %}
    
    <div class="form-group">
        <label for="{{ form.username.id_for_label }}">Username:</label>
        {{ form.username }}
        {% if form.username.errors %}
            <ul class="errors">
            {% for error in form.username.errors %}
                <li>{{ error }}</li>
            {% endfor %}
            </ul>
        {% endif %}
    </div>
    
    <button type="submit">Submit</button>
</form>
```

---

# PART 12: TEMPLATE SECURITY & BEST PRACTICES

## Auto-escaping

```django
{# HTML is escaped by default #}
{{ user_input }}  {# &lt;script&gt; stays safe #}

{# Mark safe only if trusted #}
{{ trusted_html|safe }}

{# Escape if needed #}
{{ untrusted|escape }}
```

---

## CSRF Protection

```django
<form method="post">
    {% csrf_token %}
    {# Form fields here #}
</form>
```

---

## Best Practices

```django
{# ❌ DON'T: Logic in templates #}
{% if user.profile.settings.notifications.email %}

{# ✅ DO: Logic in views/context processors #}
{% if show_email_notification %}

{# ❌ DON'T: Many template tags #}
{% for item in large_queryset %}

{# ✅ DO: Limit in view #}
{# View: products[:20] #}
```

---

# PART 13: PERFORMANCE OPTIMIZATION

## Caching Templates

```python
# settings.py
TEMPLATES = [
    {
        'OPTIONS': {
            'loaders': [
                ('django.template.loaders.cached.Loader', [
                    'django.template.loaders.filesystem.Loader',
                    'django.template.loaders.app_directories.Loader',
                ]),
            ],
        },
    },
]
```

---

## QuerySet in Templates

```django
{# ❌ DON'T: N+1 queries #}
{% for product in products %}
    {{ product.category.name }}
{% endfor %}

{# ✅ DO: Use select_related/prefetch_related #}
{# View: products.select_related('category') #}
```

---

## Fragment Caching

```django
{% load cache %}

{% cache 500 product_detail product.id %}
    <div class="product">
        <h2>{{ product.name }}</h2>
        <p>{{ product.description }}</p>
    </div>
{% endcache %}
```

---

# PART 14: TEMPLATE TESTING

## Testing Template Rendering

```python
from django.test import TestCase, Client
from django.urls import reverse

class TemplateRenderingTest(TestCase):
    def test_product_template_renders(self):
        response = self.client.get('/product/1/')
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'product_detail.html')
        self.assertContains(response, 'Product Name')
    
    def test_context_data(self):
        response = self.client.get('/product/1/')
        self.assertIn('product', response.context)
        self.assertEqual(response.context['product'].id, 1)
```

---

# PART 15: COMMON PATTERNS

## Pagination in Templates

```django
{% if page_obj.has_previous %}
    <a href="?page=1">First</a>
    <a href="?page={{ page_obj.previous_page_number }}">Previous</a>
{% endif %}

<span>Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}</span>

{% if page_obj.has_next %}
    <a href="?page={{ page_obj.next_page_number }}">Next</a>
    <a href="?page={{ page_obj.paginator.num_pages }}">Last</a>
{% endif %}
```

---

## List with Empty State

```django
{% if items %}
    <ul>
    {% for item in items %}
        <li>{{ item.name }}</li>
    {% endfor %}
    </ul>
{% else %}
    <p class="empty-state">No items found. <a href="/add/">Create one</a></p>
{% endif %}
```

---

## Breadcrumb Navigation

```django
{% block breadcrumb %}
    <nav class="breadcrumb">
        <a href="/">Home</a>
        {% for category in breadcrumbs %}
            / <a href="{{ category.get_absolute_url }}">{{ category.name }}</a>
        {% endfor %}
    </nav>
{% endblock %}
```

---

# PART 16: 50+ PRACTICAL EXAMPLES

## Example 1: Product Display

```django
{% extends "base.html" %}
{% load static %}

{% block title %}{{ product.name }}{% endblock %}

{% block content %}
<div class="product">
    <img src="{{ product.image.url }}" alt="{{ product.name }}">
    <h1>{{ product.name }}</h1>
    <p class="price">${{ product.price|floatformat:2 }}</p>
    <p>{{ product.description }}</p>
    
    {% if product.in_stock %}
        <button>Add to Cart</button>
    {% else %}
        <p class="badge">Out of Stock</p>
    {% endif %}
</div>
{% endblock %}
```

---

## Example 2: Conditional Content

```django
{% if user.is_authenticated %}
    <p>Welcome, {{ user.first_name }}!</p>
    <a href="/profile/">My Profile</a>
    <a href="/logout/">Logout</a>
{% else %}
    <p>You're not logged in.</p>
    <a href="/login/">Login</a> | <a href="/register/">Register</a>
{% endif %}
```

---

## Example 3: Dynamic Class

```django
<div class="product {% if product.featured %}featured{% endif %} {% if not product.in_stock %}out-of-stock{% endif %}">
    {{ product.name }}
</div>
```

---

## Example 4: Filter Usage

```django
<h1>{{ title|upper }}</h1>
<p>{{ description|truncatewords:20 }}</p>
<time>{{ created_at|date:"M d, Y" }}</time>
<p>{{ comments|length }} comment{{ comments|pluralize }}</p>
```

---

## Example 5: Loop with Conditions

```django
<ul>
{% for item in items %}
    {% if item.published %}
        <li class="{% if forloop.first %}first{% endif %}{% if forloop.last %} last{% endif %}">
            {{ item.title }}
            {% if forloop.counter|divisibleby:3 %}
                <span class="divider"></span>
            {% endif %}
        </li>
    {% endif %}
{% empty %}
    <li>No published items</li>
{% endfor %}
</ul>
```

---

# PART 17: INTERVIEW Q&A

**Q: What's the difference between {% block %} and {% include %}?**
A: `{% block %}` is for inheritance (override in child template), `{% include %}` is for reusing small components.

**Q: How do you pass context to an included template?**
A: Use `{% include 'template.html' with variable=value %}`

**Q: What is auto-escaping and why is it important?**
A: Django automatically escapes HTML in templates to prevent XSS attacks. Use `|safe` only for trusted content.

**Q: How do you access parent block content in child template?**
A: Use `{{ block.super }}`

**Q: What are context processors?**
A: Functions that add variables to every template context globally.

---

# PART 18: QUICK REFERENCE

| Tag | Usage |
|-----|-------|
| `{{ var }}` | Display variable |
| `{% if %}` | Conditional |
| `{% for %}` | Loop |
| `{% block %}` | Inheritance |
| `{% include %}` | Reuse template |
| `{% csrf_token %}` | CSRF protection |
| `{% load %}` | Load tag library |
| `{{ var\|filter }}` | Apply filter |

---

**Happy templating! Remember: Keep logic in views, keep presentation in templates.** 🎨
