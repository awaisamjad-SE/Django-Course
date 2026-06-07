# Django + DRF Complete Interview Outline

This outline is designed for Django backend interview preparation, from junior to mid-level, and covers both Django and Django REST Framework in a structured way.

## 1. Python Foundations

### 1.1 Core Python
- What is Python?
- Python features
- Python 2 vs Python 3
- Variables and naming rules
- Data types
- Type conversion

### 1.2 Operators
- Arithmetic operators
- Assignment operators
- Comparison operators
- Logical operators
- Membership operators
- Identity operators

### 1.3 Control Flow
- `if`
- `if-else`
- `if-elif-else`
- Nested conditions

### 1.4 Loops
- `for` loop
- `while` loop
- `break`
- `continue`
- `pass`
- Nested loops

### 1.5 Data Structures
- Lists
  - List methods
  - List comprehension
- Tuples
  - Immutability
  - Packing and unpacking
- Sets
  - Union
  - Intersection
  - Difference
- Dictionaries
  - CRUD operations
  - Dictionary comprehension
- Strings
  - String methods
  - String formatting
  - f-strings

## 2. Functions in Python

### 2.1 Function Basics
- Defining functions
- Return statement
- `return` vs `print`

### 2.2 Function Arguments
- Positional arguments
- Keyword arguments
- Default arguments

### 2.3 Advanced Arguments
- `*args`
- `**kwargs`

### 2.4 Scope
- Local scope
- Global scope
- `nonlocal`

### 2.5 Functional Tools
- Lambda functions
- `map()`
- `filter()`
- `reduce()`

## 3. Object-Oriented Programming

### 3.1 Class Fundamentals
- Class
- Object
- Attributes
- Methods

### 3.2 Constructor
- `__init__()`

### 3.3 Inheritance
- Single inheritance
- Multiple inheritance
- Multilevel inheritance

### 3.4 OOP Principles
- Polymorphism
- Encapsulation
- Abstraction

### 3.5 Method Types
- Instance method
- Class method
- Static method

### 3.6 Special Methods
- `__str__()`
- `__repr__()`
- `__len__()`

### 3.7 `super()` and MRO
- `super()`
- Method Resolution Order (MRO)

## 4. Advanced Python Concepts

### 4.1 Exception Handling
- `try`
- `except`
- `else`
- `finally`
- `raise`

### 4.2 Iterators and Generators
- Iterators
- Iterables
- Generators
- Generator expressions

### 4.3 Decorators
- Function decorators
- Decorator chaining
- Decorators with arguments

### 4.4 Context Managers
- `with` statement
- `__enter__()`
- `__exit__()`

### 4.5 Concurrency Basics
- Threading
- Multiprocessing
- Async programming

### 4.6 Useful Python Practices
- File handling
- Modules and packages
- Virtual environments
- Package installation

## 5. Database Fundamentals

### 5.1 SQL Basics
- `SELECT`
- `INSERT`
- `UPDATE`
- `DELETE`

### 5.2 Joins
- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL JOIN

### 5.3 Constraints
- Primary key
- Foreign key
- Unique key
- Not null
- Check constraints

### 5.4 Database Design
- Normalization
- Denormalization
- Indexing
- Transactions
- ACID properties

## 6. Django Fundamentals

### 6.1 What is Django?
- Django overview
- MVT architecture
- Django advantages
- Django vs Flask

### 6.2 Project and App Structure
- `manage.py`
- `settings.py`
- `urls.py`
- `apps.py`
- `wsgi.py`
- `asgi.py`

### 6.3 Environment Setup
- Virtual environment
- Package installation
- `pip` and requirements files

### 6.4 Django Request Flow
- Request lifecycle
- URL routing
- View execution
- Response generation

### 6.5 WSGI vs ASGI
- WSGI basics
- ASGI basics
- Sync vs async support

## 7. URLs, Views, and Routing

### 7.1 URL Configuration
- `path()`
- `re_path()`
- `include()`
- Namespacing

### 7.2 Views
- Function-based views
- Class-based views
- Generic views
- View decorators

### 7.3 Dynamic URLs
- URL parameters
- Slugs
- Query parameters

### 7.4 Response Handling
- `HttpResponse`
- `JsonResponse`
- Redirects
- `render()`

## 8. Templates

### 8.1 Template Engine
- Template rendering
- Context data

### 8.2 Template Tags
- `if`
- `for`
- `block`
- `extends`
- `include`

### 8.3 Template Filters
- Built-in filters
- Custom filters

### 8.4 Template Best Practices
- Template inheritance
- Reusable partials
- Separation of concerns

## 9. Static and Media Files

### 9.1 Static Files
- CSS
- JavaScript
- Images
- `STATIC_URL`
- `STATICFILES_DIRS`
- `collectstatic`

### 9.2 Media Files
- File uploads
- Image uploads
- `MEDIA_URL`
- `MEDIA_ROOT`

## 10. Django Models

### 10.1 Model Basics
- Model definition
- Fields
- Field options
- `Meta` class

### 10.2 Relationships
- One-to-one
- One-to-many
- Many-to-many
- `related_name`

### 10.3 Model Methods
- `__str__()`
- Custom model methods
- `save()` override
- `clean()` and validation hooks

### 10.4 Model Managers
- Default manager
- Custom manager
- Custom queryset methods

### 10.5 Common Model Topics
- Abstract models
- Proxy models
- Multi-table inheritance

## 11. Django ORM

### 11.1 CRUD Operations
- `create()`
- `save()`
- `get_or_create()`
- `get()`
- `filter()`
- `exclude()`
- `all()`
- `update()`
- `delete()`

### 11.2 QuerySet Methods
- `first()`
- `last()`
- `exists()`
- `count()`
- `values()`
- `values_list()`
- `distinct()`

### 11.3 ORM Lookups
- `__icontains`
- `__startswith`
- `__endswith`
- `__in`
- `__range`
- `__gt`, `__gte`, `__lt`, `__lte`

### 11.4 Query Building
- `Q` objects
- `F` expressions
- Aggregation
- Annotation
- `Subquery`
- `Exists`

### 11.5 Performance Optimization
- `select_related()`
- `prefetch_related()`
- N+1 query problem
- Bulk operations
  - `bulk_create()`
  - `bulk_update()`

## 12. Migrations

### 12.1 Migration Commands
- `makemigrations`
- `migrate`
- `showmigrations`

### 12.2 Migration Workflow
- Schema changes
- Migration files
- Reversing migrations
- Common migration issues

## 13. Forms and Validation

### 13.1 Django Forms
- Form class
- Form fields
- Form rendering

### 13.2 Model Forms
- `ModelForm`
- `Meta` inside forms
- CRUD with forms

### 13.3 Validation
- Field validation
- Form validation
- `clean()`
- `clean_<fieldname>()`

## 14. Authentication and Authorization

### 14.1 Authentication
- Login
- Logout
- `request.user`
- Session-based authentication

### 14.2 Authorization
- Permissions
- Groups
- Role-based access control

### 14.3 User Models
- Default user model
- Custom user model
- Custom user manager

### 14.4 Security and Auth Concepts
- Password hashing
- Password reset flow
- Account verification basics

### 14.5 JWT Overview
- Access token
- Refresh token
- Token expiry

## 15. Django Middleware, Sessions, and Signals

### 15.1 Middleware
- What middleware is
- Middleware order
- Built-in middleware
- Custom middleware

### 15.2 Sessions
- Session storage
- Session settings
- Session lifecycle

### 15.3 Messages Framework
- Success messages
- Error messages
- Flash messages

### 15.4 Signals
- `pre_save`
- `post_save`
- `pre_delete`
- `post_delete`

## 16. Django Admin

### 16.1 Admin Basics
- Registering models
- Admin site configuration

### 16.2 Admin Customization
- List display
- Search fields
- Filters
- Ordering
- Inline models

## 17. Django Security

### 17.1 Common Security Topics
- CSRF protection
- XSS prevention
- SQL injection prevention
- Clickjacking protection

### 17.2 Secure Settings
- `DEBUG`
- `ALLOWED_HOSTS`
- Secret key handling
- Environment variables

### 17.3 File and Upload Safety
- File validation
- Media restrictions
- Safe handling of user input

## 18. Testing in Django

### 18.1 Testing Basics
- Unit tests
- Integration tests
- Test runner

### 18.2 Django Test Tools
- `TestCase`
- `Client`
- `RequestFactory`

### 18.3 What to Test
- Models
- Views
- Forms
- APIs

## 19. Deployment and Production

### 19.1 Production Setup
- `DEBUG = False`
- Allowed hosts
- Static file handling
- Media file handling

### 19.2 Deployment Components
- Web server
- Application server
- Reverse proxy
- Database server

### 19.3 Common Deployment Topics
- Environment variables
- Logging
- Error monitoring
- Caching

## 20. Performance and Scalability

### 20.1 Query Optimization
- Reduce database hits
- Use indexes wisely
- Avoid N+1 queries

### 20.2 Caching
- Per-view cache
- Low-level cache
- Template fragment cache

### 20.3 Background Work
- Celery basics
- Task queues
- Scheduled jobs

## 21. Django REST Framework (DRF)

### 21.1 REST Basics
- What is REST?
- Resource-based design
- HTTP methods
- HTTP status codes

### 21.2 DRF Introduction
- Why DRF?
- Django vs DRF
- Serializer-driven APIs

### 21.3 Serializers
- `Serializer`
- `ModelSerializer`
- Serializer fields
- `to_representation()`
- `create()`
- `update()`

### 21.4 Validation in DRF
- Field-level validation
- Object-level validation
- `validate_<field>()`
- `validate()`
- Custom validators

### 21.5 Relationships in Serializers
- Nested serializers
- Primary key related fields
- Slug related fields
- String related fields

### 21.6 Views in DRF
- `APIView`
- `GenericAPIView`
- Mixins
- Generic class-based views
- `ViewSet`
- `ModelViewSet`

### 21.7 Routers
- `DefaultRouter`
- Custom routes
- Nested routing basics

### 21.8 Authentication in DRF
- Session authentication
- Token authentication
- JWT authentication
- Custom authentication classes

### 21.9 Permissions
- `IsAuthenticated`
- `AllowAny`
- `IsAdminUser`
- Custom permissions
- Object-level permissions

### 21.10 Throttling
- Rate limiting
- User throttles
- Anonymous throttles
- Custom throttle classes

### 21.11 Filtering and Searching
- Filtering
- Search filter
- Ordering filter
- Custom filter backends

### 21.12 Pagination
- Page number pagination
- Limit-offset pagination
- Cursor pagination

### 21.13 Parsers and Renderers
- JSON parser
- Form parser
- MultiPart parser
- JSON renderer
- Browsable API

### 21.14 Content Negotiation
- Request content type
- Response format selection

### 21.15 Exception Handling
- Validation errors
- API exceptions
- Custom exception handler

### 21.16 Versioning
- URL path versioning
- Query parameter versioning
- Header-based versioning

### 21.17 API Documentation
- Schema generation
- Swagger/OpenAPI
- ReDoc

### 21.18 DRF Testing
- API test cases
- APIClient
- Authentication in tests
- Serializer tests
- View tests

## 22. Real-World DRF Patterns

### 22.1 CRUD API Design
- List endpoint
- Detail endpoint
- Create endpoint
- Update endpoint
- Delete endpoint

### 22.2 Nested and Related APIs
- Parent-child resources
- Writable nested serializers
- Relationship handling

### 22.3 File Upload APIs
- Multipart requests
- Image/file serializers
- Validation for uploads

### 22.4 Auth APIs
- Signup
- Login
- Token refresh
- Password reset
- Profile endpoints

### 22.5 Common Business Features
- Search
- Sort
- Filter
- Pagination
- Soft delete
- Audit fields

## 23. Asynchronous Django Topics

### 23.1 Async Support
- Async views
- Async middleware basics
- When to use async

### 23.2 ASGI Ecosystem
- WebSockets basics
- Real-time communication
- Channel layer overview

## 24. Interview-Focused Revision List

### 24.1 Must-Know Django Topics
- Models and ORM
- Migrations
- Authentication and permissions
- Middleware
- Sessions
- Security
- Testing

### 24.2 Must-Know DRF Topics
- Serializers
- ViewSets and routers
- Authentication
- Permissions
- Pagination
- Filtering
- Exception handling

### 24.3 Common Interview Questions
- Explain MVT in Django
- Difference between `select_related()` and `prefetch_related()`
- Why use `ModelSerializer`?
- Difference between `APIView` and `ViewSet`
- How does JWT authentication work?
- How do you optimize a slow API?

## 25. Practice Project Ideas

### 25.1 Beginner Projects
- Blog application
- To-do application
- Student management system

### 25.2 Intermediate Projects
- E-commerce API
- Task management system
- Inventory management system

### 25.3 Advanced Projects
- Multi-role API platform
- Chat or notification system
- Analytics dashboard backend
