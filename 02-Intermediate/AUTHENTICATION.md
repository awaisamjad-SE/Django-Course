# 🔐 DJANGO AUTHENTICATION - COMPLETE TUTORIAL A-Z

**Master User Authentication, Permissions, Authorization, and Groups**

---

## 📖 TABLE OF CONTENTS

1. Authentication Fundamentals
2. User Model & Management
3. Login & Logout
4. Registration & Sign Up
5. Password Management
6. Permissions System
7. Groups & Roles
8. Decorators & Mixins
9. Session Management
10. Token Authentication (DRF)
11. Social Authentication
12. Two-Factor Authentication
13. Custom User Model
14. Role-Based Access Control
15. Testing Authentication
16. 50+ Practical Examples
17. Interview Q&A
18. Quick Reference

---

# PART 1: AUTHENTICATION FUNDAMENTALS

## What is Authentication?

Authentication verifies that a user is who they claim to be. It validates credentials (username/password).

### Authentication vs Authorization

```
Authentication: "Are you who you say you are?" (Login)
Authorization: "What are you allowed to do?" (Permissions)
```

---

## Built-in User Model

```python
from django.contrib.auth.models import User

# Create user
user = User.objects.create_user(
    username='john',
    email='john@example.com',
    password='securepass123'
)

# Create superuser
admin = User.objects.create_superuser(
    username='admin',
    email='admin@example.com',
    password='adminpass123'
)

# Check authentication
user.is_authenticated  # True
user.is_active         # True
user.is_staff          # False
user.is_superuser      # False
```

---

## User Fields

```python
user.username           # Username
user.first_name         # First name
user.last_name          # Last name
user.email              # Email address
user.password           # Hashed password
user.is_staff           # Can access admin
user.is_active          # Account active
user.is_superuser       # Full permissions
user.date_joined        # Registration date
user.last_login         # Last login date
user.groups             # User groups
user.user_permissions   # User permissions
```

---

# PART 2: USER MODEL & MANAGEMENT

## User Manager Methods

```python
# Create user
user = User.objects.create_user(
    username='john',
    email='john@example.com',
    password='password123'
)

# Get user
user = User.objects.get(username='john')
user = User.objects.get_by_natural_key('john')

# Check if user exists
exists = User.objects.filter(username='john').exists()

# Update user
user.email = 'new@example.com'
user.save()

# Delete user
user.delete()

# Get all users
all_users = User.objects.all()
active_users = User.objects.filter(is_active=True)
```

---

## Change Password

```python
from django.contrib.auth import update_session_auth_hash

user.set_password('newpassword123')
user.save()

# Keep user logged in after password change
update_session_auth_hash(request, user)
```

---

## Check Password

```python
if user.check_password('provided_password'):
    # Password matches
else:
    # Password incorrect
```

---

# PART 3: LOGIN & LOGOUT

## Login View

```python
from django.contrib.auth import authenticate, login
from django.contrib.auth.forms import AuthenticationForm
from django.shortcuts import render, redirect

def login_view(request):
    if request.method == 'POST':
        form = AuthenticationForm(request, data=request.POST)
        if form.is_valid():
            user = form.get_user()
            login(request, user)
            return redirect('home')
    else:
        form = AuthenticationForm()
    
    return render(request, 'login.html', {'form': form})
```

---

## Logout View

```python
from django.contrib.auth import logout

def logout_view(request):
    logout(request)
    return redirect('home')
```

---

## Login Required

```python
from django.contrib.auth.decorators import login_required

@login_required(login_url='login')
def protected_view(request):
    return render(request, 'protected.html')

# In template
{% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}!</p>
{% else %}
    <p><a href="/login/">Login</a></p>
{% endif %}
```

---

## Class-Based Login View

```python
from django.contrib.auth.views import LoginView, LogoutView
from django.urls import reverse_lazy

class MyLoginView(LoginView):
    template_name = 'login.html'
    success_url = reverse_lazy('home')

class MyLogoutView(LogoutView):
    next_page = reverse_lazy('home')
```

---

# PART 4: REGISTRATION & SIGN UP

## Registration Form

```python
from django import forms
from django.contrib.auth.models import User

class RegistrationForm(forms.ModelForm):
    password = forms.CharField(widget=forms.PasswordInput)
    password_confirm = forms.CharField(widget=forms.PasswordInput)
    
    class Meta:
        model = User
        fields = ['username', 'email', 'password']
    
    def clean_username(self):
        username = self.cleaned_data['username']
        if User.objects.filter(username=username).exists():
            raise forms.ValidationError('Username already taken')
        return username
    
    def clean(self):
        cleaned_data = super().clean()
        password = cleaned_data.get('password')
        password_confirm = cleaned_data.get('password_confirm')
        
        if password != password_confirm:
            raise forms.ValidationError('Passwords do not match')
        
        return cleaned_data
    
    def save(self, commit=True):
        user = super().save(commit=False)
        user.set_password(self.cleaned_data['password'])
        if commit:
            user.save()
        return user
```

---

## Registration View

```python
def register_view(request):
    if request.method == 'POST':
        form = RegistrationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect('home')
    else:
        form = RegistrationForm()
    
    return render(request, 'register.html', {'form': form})
```

---

## Email Verification

```python
from django.core.mail import send_mail
from django.contrib.auth.tokens import default_token_generator
from django.utils.http import urlsafe_base64_encode, urlsafe_base64_decode
from django.utils.encoding import force_bytes, force_str

def send_verification_email(request, user):
    token = default_token_generator.make_token(user)
    uid = urlsafe_base64_encode(force_bytes(user.pk))
    
    verification_url = request.build_absolute_uri(
        reverse('verify_email', kwargs={'uidb64': uid, 'token': token})
    )
    
    send_mail(
        'Verify Your Email',
        f'Click here to verify: {verification_url}',
        'from@example.com',
        [user.email],
    )

def verify_email(request, uidb64, token):
    try:
        uid = force_str(urlsafe_base64_decode(uidb64))
        user = User.objects.get(pk=uid)
    except:
        return render(request, 'verification_failed.html')
    
    if default_token_generator.check_token(user, token):
        user.is_active = True
        user.save()
        return render(request, 'verification_success.html')
    else:
        return render(request, 'verification_failed.html')
```

---

# PART 5: PASSWORD MANAGEMENT

## Password Change

```python
from django.contrib.auth.views import PasswordChangeView
from django.urls import reverse_lazy

class MyPasswordChangeView(PasswordChangeView):
    template_name = 'change_password.html'
    success_url = reverse_lazy('password_change_done')
```

---

## Password Reset

```python
from django.contrib.auth.views import PasswordResetView, PasswordResetConfirmView

class MyPasswordResetView(PasswordResetView):
    template_name = 'password_reset.html'
    email_template_name = 'password_reset_email.html'
    success_url = reverse_lazy('password_reset_done')

class MyPasswordResetConfirmView(PasswordResetConfirmView):
    template_name = 'password_reset_confirm.html'
    success_url = reverse_lazy('password_reset_complete')
```

---

## Password Validators

```python
# settings.py
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        'OPTIONS': {
            'min_length': 8,
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

# PART 6: PERMISSIONS SYSTEM

## Permission Basics

```python
from django.contrib.auth.models import Permission
from django.contrib.contenttypes.models import ContentType

# View permissions
permissions = Permission.objects.filter(content_type__app_label='myapp')

# Add permission to user
from django.contrib.auth.models import User
user = User.objects.get(username='john')
permission = Permission.objects.get(codename='can_publish_post')
user.user_permissions.add(permission)

# Check permission
if user.has_perm('myapp.can_publish_post'):
    # User has permission
```

---

## Permission Decorators

```python
from django.contrib.auth.decorators import permission_required

@permission_required('myapp.can_publish_post', login_url='login')
def publish_post(request):
    # Only users with permission can access
    return render(request, 'publish.html')

@permission_required(['myapp.can_publish_post', 'myapp.can_edit_post'])
def edit_post(request):
    # Multiple permissions
    pass
```

---

## Permission Mixins (CBV)

```python
from django.contrib.auth.mixins import PermissionRequiredMixin

class PublishPostView(PermissionRequiredMixin, DetailView):
    permission_required = 'myapp.can_publish_post'
    login_url = 'login'
    model = Post
    template_name = 'publish_post.html'

class EditPostView(PermissionRequiredMixin, UpdateView):
    permission_required = ['myapp.can_edit_post']
    model = Post
    template_name = 'edit_post.html'
```

---

## Custom Permissions in Models

```python
class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    
    class Meta:
        permissions = [
            ('can_publish_post', 'Can publish posts'),
            ('can_edit_others_post', 'Can edit other users posts'),
        ]
```

---

# PART 7: GROUPS & ROLES

## Group Management

```python
from django.contrib.auth.models import Group, Permission

# Create group
editors_group = Group.objects.create(name='Editors')

# Add permissions to group
can_edit_post = Permission.objects.get(codename='can_edit_post')
can_publish_post = Permission.objects.get(codename='can_publish_post')
editors_group.permissions.add(can_edit_post, can_publish_post)

# Add user to group
user = User.objects.get(username='john')
user.groups.add(editors_group)

# Check if user in group
if user.groups.filter(name='Editors').exists():
    # User is in Editors group
```

---

## Role-Based Access Control

```python
from django.contrib.auth.decorators import user_passes_test

def is_editor(user):
    return user.groups.filter(name='Editors').exists()

@user_passes_test(is_editor)
def edit_post(request):
    return render(request, 'edit_post.html')

# Class-based
from django.contrib.auth.mixins import UserPassesTestMixin

class EditPostView(UserPassesTestMixin, UpdateView):
    model = Post
    
    def test_func(self):
        return self.request.user.groups.filter(name='Editors').exists()
```

---

# PART 8: DECORATORS & MIXINS

## Login Required Decorator

```python
@login_required
def dashboard(request):
    return render(request, 'dashboard.html')

# Redirect to specific login page
@login_required(login_url='custom_login')
def dashboard(request):
    pass
```

---

## Permission Mixins

```python
from django.contrib.auth.mixins import LoginRequiredMixin, UserPassesTestMixin

class DashboardView(LoginRequiredMixin, TemplateView):
    template_name = 'dashboard.html'
    login_url = 'login'

class UserDetailView(LoginRequiredMixin, DetailView):
    def test_func(self):
        user = self.get_object()
        return self.request.user == user
```

---

# PART 9: SESSION MANAGEMENT

## Session Data

```python
# Store session data
request.session['user_preference'] = 'dark_mode'
request.session['cart_items'] = [1, 2, 3]

# Retrieve session data
preference = request.session.get('user_preference')

# Delete session data
del request.session['user_preference']

# Clear session
request.session.flush()

# Set expiry
request.session.set_expiry(600)  # 10 minutes
```

---

## Session Settings

```python
# settings.py
SESSION_ENGINE = 'django.contrib.sessions.backends.db'
SESSION_COOKIE_AGE = 1209600  # 2 weeks
SESSION_COOKIE_SECURE = True  # HTTPS only
SESSION_COOKIE_HTTPONLY = True
SESSION_COOKIE_SAMESITE = 'Strict'
```

---

# PART 10: TOKEN AUTHENTICATION (DRF)

## Token Setup

```python
# settings.py
INSTALLED_APPS = [
    'rest_framework',
    'rest_framework.authtoken',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
}
```

---

## Generate Token

```python
from rest_framework.authtoken.models import Token

# Create token for user
token = Token.objects.create(user=user)

# Get existing token
token = Token.objects.get(user=user)
```

---

## API View with Token Auth

```python
from rest_framework.decorators import api_view, authentication_classes, permission_classes
from rest_framework.authentication import TokenAuthentication
from rest_framework.permissions import IsAuthenticated

@api_view(['GET'])
@authentication_classes([TokenAuthentication])
@permission_classes([IsAuthenticated])
def protected_endpoint(request):
    return Response({'message': f'Hello, {request.user.username}'})
```

---

# PART 11: SOCIAL AUTHENTICATION

## OAuth2 Setup (Django-Allauth)

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.sites',
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.google',
]

SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': [
            'profile',
            'email',
        ],
        'AUTH_PARAMS': {
            'access_type': 'online',
        }
    }
}
```

---

## Template Usage

```django
{% load socialaccount %}

<a href="{% provider_login_url "google" %}">Login with Google</a>
```

---

# PART 12: TWO-FACTOR AUTHENTICATION

## 2FA Implementation

```python
from django_otp.decorators import otp_required
from django_otp.middleware import OTPMiddleware

# settings.py
MIDDLEWARE = [
    'django_otp.middleware.OTPMiddleware',
]

@otp_required
def sensitive_view(request):
    return render(request, 'sensitive.html')
```

---

# PART 13: CUSTOM USER MODEL

## Define Custom User

```python
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    phone = models.CharField(max_length=15)
    bio = models.TextField(blank=True)
    profile_picture = models.ImageField(upload_to='profiles/', blank=True)
    
    class Meta:
        verbose_name = 'User'
        verbose_name_plural = 'Users'
```

---

## Configure in Settings

```python
# settings.py
AUTH_USER_MODEL = 'myapp.CustomUser'
```

---

# PART 14: ROLE-BASED ACCESS CONTROL

## Implement RBAC

```python
ROLES = {
    'admin': ['can_publish', 'can_edit', 'can_delete'],
    'editor': ['can_publish', 'can_edit'],
    'author': ['can_write'],
    'viewer': []
}

def get_user_role(user):
    if user.is_superuser:
        return 'admin'
    
    for group in user.groups.all():
        if group.name.lower() in ROLES:
            return group.name.lower()
    
    return 'viewer'
```

---

# PART 15: TESTING AUTHENTICATION

## Authentication Tests

```python
from django.test import TestCase, Client
from django.contrib.auth.models import User

class AuthenticationTest(TestCase):
    def setUp(self):
        self.client = Client()
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass123'
        )
    
    def test_login(self):
        response = self.client.post('/login/', {
            'username': 'testuser',
            'password': 'testpass123'
        })
        self.assertEqual(response.status_code, 302)
    
    def test_protected_view_requires_login(self):
        response = self.client.get('/dashboard/')
        self.assertEqual(response.status_code, 302)
    
    def test_authenticated_user_access(self):
        self.client.login(username='testuser', password='testpass123')
        response = self.client.get('/dashboard/')
        self.assertEqual(response.status_code, 200)
```

---

# PART 16: 50+ PRACTICAL EXAMPLES

## Example 1: Complete Authentication

```python
# views.py
def login_view(request):
    if request.user.is_authenticated:
        return redirect('home')
    
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        user = authenticate(username=username, password=password)
        
        if user is not None:
            login(request, user)
            return redirect('dashboard')
        else:
            context = {'error': 'Invalid credentials'}
            return render(request, 'login.html', context)
    
    return render(request, 'login.html')
```

---

# PART 17: INTERVIEW Q&A

**Q: What's the difference between authentication and authorization?**
A: Authentication verifies identity (login). Authorization checks permissions (what you can do).

**Q: How are passwords stored?**
A: Django uses PBKDF2 hash algorithm. Passwords are never stored in plain text.

**Q: What's the difference between user.is_active and user.is_staff?**
A: is_active means user can log in. is_staff means user can access admin panel.

---

# PART 18: QUICK REFERENCE

| Function | Purpose |
|----------|---------|
| `authenticate()` | Verify credentials |
| `login()` | Create session |
| `logout()` | Destroy session |
| `@login_required` | Protect view |
| `has_perm()` | Check permission |
| `user.groups` | User groups |

---

**Secure your Django applications with robust authentication!** 🔐
