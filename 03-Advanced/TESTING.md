# 🧪 DJANGO TESTING - COMPLETE TUTORIAL A-Z

**Master Unit Tests, Integration Tests, Test Fixtures, and Coverage**

---

## 📖 TABLE OF CONTENTS

1. Testing Fundamentals
2. Test Case Types
3. Writing Tests
4. Test Client
5. Fixtures & Factories
6. Database Testing
7. Model Testing
8. View Testing
9. Form Testing
10. API Testing
11. Middleware Testing
12. Test Coverage
13. Test Organization
14. Continuous Integration
15. 40+ Practical Examples
16. Interview Q&A
17. Quick Reference

---

# PART 1: TESTING FUNDAMENTALS

## Why Test?

Testing ensures code quality, prevents regressions, and builds confidence.

### Testing Pyramid

```
         /\
        /UI\
       /Tests\
      /-------\
     /Integration\
    /   Tests    \
   /-------------\
  /     Unit      \
 /      Tests      \
/------------------\
```

---

## Test Types

```
Unit Tests: Individual functions/methods
Integration Tests: Multiple components together
End-to-End Tests: Full user workflows
```

---

# PART 2: TEST CASE TYPES

## TestCase

```python
from django.test import TestCase

class ProductTestCase(TestCase):
    def setUp(self):
        # Create test data
        self.product = Product.objects.create(
            name='Test Product',
            price=9.99
        )
    
    def test_product_creation(self):
        # Test assertions
        self.assertEqual(self.product.name, 'Test Product')
    
    def tearDown(self):
        # Cleanup (optional - happens automatically)
        pass
```

---

## TransactionTestCase

```python
from django.test import TransactionTestCase

class TransactionTest(TransactionTestCase):
    # Use when testing transactions
    def test_transaction_rollback(self):
        with transaction.atomic():
            Product.objects.create(name='Test')
            raise Exception("Rollback!")
```

---

## SimpleTestCase

```python
from django.test import SimpleTestCase

class SimpleTest(SimpleTestCase):
    # No database access
    def test_calculation(self):
        result = 2 + 2
        self.assertEqual(result, 4)
```

---

# PART 3: WRITING TESTS

## Test Structure

```python
from django.test import TestCase
from .models import Product
from .forms import ProductForm

class ProductTest(TestCase):
    def setUp(self):
        self.product = Product.objects.create(
            name='Laptop',
            price=999.99
        )
    
    def test_string_representation(self):
        self.assertEqual(str(self.product), 'Laptop')
    
    def test_product_price(self):
        self.assertEqual(self.product.price, 999.99)
```

---

## Common Assertions

```python
# Equality
self.assertEqual(a, b)
self.assertNotEqual(a, b)

# Truth
self.assertTrue(value)
self.assertFalse(value)

# Membership
self.assertIn(item, list)
self.assertNotIn(item, list)

# Count
self.assertEqual(queryset.count(), 5)

# Exceptions
with self.assertRaises(ValueError):
    bad_function()

# Objects
self.assertIsNone(value)
self.assertIsNotNone(value)
self.assertIs(a, b)
self.assertIsNot(a, b)
```

---

# PART 4: TEST CLIENT

## Request Testing

```python
from django.test import Client

class ViewTest(TestCase):
    def setUp(self):
        self.client = Client()
    
    def test_product_list_view(self):
        response = self.client.get('/products/')
        
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'products/list.html')
```

---

## POST Requests

```python
def test_create_product(self):
    response = self.client.post('/products/create/', {
        'name': 'New Product',
        'price': 29.99
    })
    
    self.assertEqual(response.status_code, 302)  # Redirect
    self.assertTrue(Product.objects.filter(name='New Product').exists())
```

---

## Authentication

```python
def setUp(self):
    self.user = User.objects.create_user(
        username='testuser',
        password='testpass123'
    )
    self.client = Client()

def test_authenticated_view(self):
    self.client.login(username='testuser', password='testpass123')
    response = self.client.get('/dashboard/')
    self.assertEqual(response.status_code, 200)
```

---

# PART 5: FIXTURES & FACTORIES

## Fixtures (JSON)

```json
[
    {
        "model": "myapp.product",
        "pk": 1,
        "fields": {
            "name": "Test Product",
            "price": "9.99"
        }
    }
]
```

---

## Load Fixtures

```python
from django.test import TestCase

class ProductTest(TestCase):
    fixtures = ['products.json']
    
    def test_with_fixture(self):
        product = Product.objects.get(name='Test Product')
        self.assertEqual(product.price, 9.99)
```

---

## Factories (Better Alternative)

```python
import factory

class ProductFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Product
    
    name = 'Test Product'
    price = factory.Faker('pydecimal', left_digits=3, right_digits=2)
    description = factory.Faker('text')

# Usage
product = ProductFactory()
products = ProductFactory.create_batch(10)
```

---

# PART 6: DATABASE TESTING

## Database Queries

```python
from django.test.utils import override_settings
from django.test import TransactionTestCase
from django.db import connection
from django.test.utils import CaptureQueriesContext

class QueryTest(TransactionTestCase):
    def test_query_count(self):
        with self.assertNumQueries(1):
            list(Product.objects.all())
    
    def test_capture_queries(self):
        with CaptureQueriesContext(connection) as context:
            list(Product.objects.all())
        
        self.assertEqual(len(context), 1)
```

---

## Transaction Testing

```python
from django.db import transaction

class TransactionTest(TransactionTestCase):
    def test_atomic_transaction(self):
        with transaction.atomic():
            Product.objects.create(name='Test1')
            Product.objects.create(name='Test2')
        
        self.assertEqual(Product.objects.count(), 2)
```

---

# PART 7: MODEL TESTING

## Model Tests

```python
class ProductModelTest(TestCase):
    def setUp(self):
        self.product = Product.objects.create(
            name='Laptop',
            price=999.99
        )
    
    def test_str_representation(self):
        self.assertEqual(str(self.product), 'Laptop')
    
    def test_get_absolute_url(self):
        url = self.product.get_absolute_url()
        self.assertTrue(url.startswith('/products/'))
    
    def test_discount_price_calculation(self):
        self.product.discount_percent = 10
        expected = 899.99
        self.assertAlmostEqual(
            self.product.get_discount_price(),
            expected,
            places=2
        )
```

---

## Validator Tests

```python
class CustomValidatorTest(TestCase):
    def test_valid_email(self):
        from .validators import validate_company_email
        
        # Should not raise
        validate_company_email('user@company.com')
    
    def test_invalid_email(self):
        from .validators import validate_company_email
        
        with self.assertRaises(ValidationError):
            validate_company_email('user@external.com')
```

---

# PART 8: VIEW TESTING

## Class-Based View Tests

```python
class ProductListViewTest(TestCase):
    def setUp(self):
        Product.objects.create(name='Product 1')
        Product.objects.create(name='Product 2')
    
    def test_view_url_exists(self):
        response = self.client.get('/products/')
        self.assertEqual(response.status_code, 200)
    
    def test_view_uses_correct_template(self):
        response = self.client.get('/products/')
        self.assertTemplateUsed(response, 'products/list.html')
    
    def test_context_data(self):
        response = self.client.get('/products/')
        self.assertEqual(len(response.context['products']), 2)
```

---

## Function-Based View Tests

```python
class ProductDetailViewTest(TestCase):
    def setUp(self):
        self.product = Product.objects.create(
            name='Test Product',
            price=29.99
        )
    
    def test_view_returns_correct_product(self):
        response = self.client.get(f'/products/{self.product.id}/')
        self.assertEqual(response.context['product'], self.product)
```

---

# PART 9: FORM TESTING

## Form Tests

```python
class ProductFormTest(TestCase):
    def test_valid_form(self):
        form_data = {
            'name': 'Test Product',
            'price': '29.99'
        }
        form = ProductForm(data=form_data)
        self.assertTrue(form.is_valid())
    
    def test_empty_form(self):
        form = ProductForm(data={})
        self.assertFalse(form.is_valid())
    
    def test_invalid_price(self):
        form_data = {
            'name': 'Test',
            'price': 'invalid'
        }
        form = ProductForm(data=form_data)
        self.assertFalse(form.is_valid())
        self.assertIn('price', form.errors)
```

---

# PART 10: API TESTING

## REST API Tests

```python
from rest_framework.test import APITestCase

class ProductAPITest(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass'
        )
        self.product = Product.objects.create(
            name='Test API Product',
            price=49.99
        )
    
    def test_list_products(self):
        response = self.client.get('/api/products/')
        self.assertEqual(response.status_code, 200)
    
    def test_create_product(self):
        self.client.force_authenticate(user=self.user)
        
        response = self.client.post('/api/products/', {
            'name': 'New Product',
            'price': 39.99
        })
        
        self.assertEqual(response.status_code, 201)
        self.assertTrue(Product.objects.filter(name='New Product').exists())
```

---

# PART 11: MIDDLEWARE TESTING

## Middleware Tests

```python
from django.test import RequestFactory

class MiddlewareTest(TestCase):
    def setUp(self):
        self.factory = RequestFactory()
    
    def test_middleware_adds_header(self):
        from .middleware import MyMiddleware
        
        def dummy_view(request):
            return HttpResponse('OK')
        
        middleware = MyMiddleware(dummy_view)
        request = self.factory.get('/')
        response = middleware(request)
        
        self.assertIn('X-Custom-Header', response)
```

---

# PART 12: TEST COVERAGE

## Coverage Configuration

```bash
# Install coverage
pip install coverage

# Run tests with coverage
coverage run --source='.' manage.py test

# Generate report
coverage report

# Generate HTML report
coverage html
```

---

## Coverage Files

```
.coveragerc
[run]
source = .
omit = */migrations/*,*/venv/*

[report]
exclude_lines =
    pragma: no cover
    def __repr__
    raise AssertionError
```

---

# PART 13: TEST ORGANIZATION

## Test Structure

```
myapp/
├── tests/
│   ├── __init__.py
│   ├── test_models.py
│   ├── test_views.py
│   ├── test_forms.py
│   ├── test_api.py
│   └── fixtures/
│       └── test_data.json
└── models.py
```

---

# PART 14: CONTINUOUS INTEGRATION

## GitHub Actions

```yaml
name: Django Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.9
    
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
    
    - name: Run tests
      run: |
        python manage.py test
```

---

# PART 15: 40+ PRACTICAL EXAMPLES

## Example: Complete Test Suite

```python
class ProductTestSuite(TestCase):
    fixtures = ['products.json']
    
    @classmethod
    def setUpClass(cls):
        super().setUpClass()
        # Run once for all tests
    
    def setUp(self):
        self.product = Product.objects.first()
    
    def test_model_creation(self):
        self.assertIsNotNone(self.product)
    
    def test_model_str(self):
        self.assertEqual(str(self.product), self.product.name)
    
    def test_view_access(self):
        response = self.client.get(self.product.get_absolute_url())
        self.assertEqual(response.status_code, 200)
```

---

# PART 16: INTERVIEW Q&A

**Q: What's the difference between unit and integration tests?**
A: Unit tests test individual components. Integration tests test components together.

**Q: What's a fixture?**
A: Fixture is test data loaded before tests run.

**Q: How do you test views?**
A: Use TestClient to make requests and assert response status/context.

---

# PART 17: QUICK REFERENCE

| Method | Purpose |
|--------|---------|
| `assertEqual()` | Compare values |
| `assertTrue()` | Assert true |
| `assertIn()` | Check membership |
| `assertRaises()` | Check exception |
| `assertTemplateUsed()` | Check template |
| `setUp()` | Before each test |
| `tearDown()` | After each test |

---

**Write tests and build confidence in your Django code!** 🧪
