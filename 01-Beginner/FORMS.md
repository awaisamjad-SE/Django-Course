# 📋 DJANGO FORMS - COMPLETE TUTORIAL A-Z

**Master Form Creation, Validation, Handling, and ModelForms**

---

## 📖 TABLE OF CONTENTS

1. Form Fundamentals
2. Form Fields - Complete Guide
3. Form Widgets
4. Form Validation
5. Custom Validation
6. ModelForms
7. Form Inheritance
8. Formsets & Model Formsets
9. AJAX Form Submission
10. Form Security
11. Error Handling & Display
12. File Upload Handling
13. Dynamic Forms
14. Testing Forms
15. Common Patterns
16. 50+ Practical Examples
17. Interview Q&A
18. Quick Reference

---

# PART 1: FORM FUNDAMENTALS

## What are Forms?

Forms handle user input collection, validation, and processing. They bridge templates and models.

### Form vs ModelForm

```python
# Regular Form - Manual fields
class ContactForm(forms.Form):
    name = forms.CharField()
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)

# ModelForm - Auto-generated from model
class ProductForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = ['name', 'price', 'description']
```

---

## Basic Form Handling in Views

```python
from django.shortcuts import render, redirect
from .forms import ContactForm

def contact_form_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Process form data
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            # Send email or save to DB
            return redirect('success_page')
    else:
        form = ContactForm()
    
    return render(request, 'contact.html', {'form': form})
```

---

## Class-Based View Form Handling

```python
from django.views.generic import FormView
from django.urls import reverse_lazy

class ContactFormView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = reverse_lazy('success_page')
    
    def form_valid(self, form):
        # Process valid form
        name = form.cleaned_data['name']
        # Send email, save to DB, etc.
        return super().form_valid(form)
    
    def form_invalid(self, form):
        # Handle invalid form
        return super().form_invalid(form)
```

---

# PART 2: FORM FIELDS - COMPLETE GUIDE

## Text Fields

```python
class MyForm(forms.Form):
    name = forms.CharField(
        max_length=100,
        required=True,
        label='Full Name',
        help_text='Enter your full name'
    )
    
    description = forms.CharField(
        max_length=500,
        widget=forms.Textarea(attrs={'rows': 4}),
        required=False
    )
    
    slug = forms.SlugField()  # Alphanumeric + underscore/hyphen
```

---

## Numeric Fields

```python
class MyForm(forms.Form):
    age = forms.IntegerField(min_value=0, max_value=150)
    
    price = forms.DecimalField(
        max_digits=10,
        decimal_places=2,
        min_value=0
    )
    
    rating = forms.FloatField(min_value=0, max_value=5)
```

---

## Date & Time Fields

```python
class MyForm(forms.Form):
    birth_date = forms.DateField(
        widget=forms.DateInput(attrs={'type': 'date'})
    )
    
    appointment_time = forms.TimeField(
        widget=forms.TimeInput(attrs={'type': 'time'})
    )
    
    created_at = forms.DateTimeField(
        widget=forms.DateTimeInput(attrs={'type': 'datetime-local'})
    )
```

---

## Choice Fields

```python
class MyForm(forms.Form):
    # Simple choices
    GENDER_CHOICES = [
        ('M', 'Male'),
        ('F', 'Female'),
        ('O', 'Other'),
    ]
    gender = forms.ChoiceField(choices=GENDER_CHOICES)
    
    # Multiple choice
    interests = forms.MultipleChoiceField(
        choices=[
            ('sports', 'Sports'),
            ('reading', 'Reading'),
            ('music', 'Music'),
        ],
        widget=forms.CheckboxSelectMultiple
    )
    
    # Dynamic choices from queryset
    category = forms.ModelChoiceField(
        queryset=Category.objects.all(),
        to_field_name='slug'
    )
    
    products = forms.ModelMultipleChoiceField(
        queryset=Product.objects.all()
    )
```

---

## Email & URL Fields

```python
class MyForm(forms.Form):
    email = forms.EmailField()
    
    website = forms.URLField(required=False)
    
    email_list = forms.CharField(
        validators=[validate_emails]  # Custom validator
    )
```

---

## File Fields

```python
class MyForm(forms.Form):
    document = forms.FileField(
        label='Upload Document',
        help_text='Accepted formats: PDF, DOC'
    )
    
    image = forms.ImageField()
    
    multiple_files = forms.FileField(
        widget=forms.ClearableFileInput(attrs={'multiple': True})
    )
```

---

## Boolean & Null Fields

```python
class MyForm(forms.Form):
    agree_to_terms = forms.BooleanField(required=True)
    
    subscribe_newsletter = forms.BooleanField(required=False)
    
    # NullBooleanField (deprecated, use BooleanField with required=False)
    notify = forms.BooleanField(
        required=False,
        label='Send notifications?'
    )
```

---

## Field Options

```python
field = forms.CharField(
    # Display
    label='Field Label',
    help_text='Helpful text here',
    label_suffix=':',
    
    # Validation
    required=True,
    max_length=100,
    min_length=3,
    
    # Initial value
    initial='Default value',
    
    # Disabled
    disabled=False,
    
    # CSS classes
    widget=forms.TextInput(attrs={
        'class': 'form-control',
        'placeholder': 'Enter value'
    })
)
```

---

# PART 3: FORM WIDGETS

## Common Widgets

```python
class MyForm(forms.Form):
    # Text input
    name = forms.CharField(widget=forms.TextInput)
    
    # Text area
    description = forms.CharField(widget=forms.Textarea)
    
    # Password
    password = forms.CharField(widget=forms.PasswordInput)
    
    # Select dropdown
    category = forms.ChoiceField(widget=forms.Select)
    
    # Radio buttons
    status = forms.ChoiceField(
        widget=forms.RadioSelect,
        choices=[('active', 'Active'), ('inactive', 'Inactive')]
    )
    
    # Checkboxes
    features = forms.MultipleChoiceField(
        widget=forms.CheckboxSelectMultiple,
        choices=[('f1', 'Feature 1'), ('f2', 'Feature 2')]
    )
    
    # Date picker
    date = forms.DateField(widget=forms.DateInput(attrs={'type': 'date'}))
    
    # Hidden field
    user_id = forms.IntegerField(widget=forms.HiddenInput)
```

---

## Custom Widget Attributes

```python
class MyForm(forms.Form):
    email = forms.EmailField(
        widget=forms.EmailInput(attrs={
            'class': 'form-control',
            'placeholder': 'your@email.com',
            'aria-label': 'Email address',
            'data-validate': 'true'
        })
    )
    
    message = forms.CharField(
        widget=forms.Textarea(attrs={
            'class': 'form-control',
            'rows': 5,
            'placeholder': 'Your message here...'
        })
    )
```

---

# PART 4: FORM VALIDATION

## Cleaned Data

```python
def contact_form_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # Access cleaned and validated data
            name = form.cleaned_data['name']
            email = form.cleaned_data['email']
            # Data is guaranteed to be valid
    return render(request, 'form.html', {'form': form})
```

---

## Field-Level Validation

```python
class MyForm(forms.Form):
    email = forms.EmailField()
    
    # Validation method
    def clean_email(self):
        email = self.cleaned_data['email']
        if User.objects.filter(email=email).exists():
            raise forms.ValidationError('Email already registered')
        return email
    
    age = forms.IntegerField()
    
    def clean_age(self):
        age = self.cleaned_data['age']
        if age < 18:
            raise forms.ValidationError('Must be 18 or older')
        return age
```

---

## Form-Level Validation

```python
class MyForm(forms.Form):
    password = forms.CharField(widget=forms.PasswordInput)
    password_confirm = forms.CharField(widget=forms.PasswordInput)
    
    # Validate across multiple fields
    def clean(self):
        cleaned_data = super().clean()
        password = cleaned_data.get('password')
        password_confirm = cleaned_data.get('password_confirm')
        
        if password and password_confirm:
            if password != password_confirm:
                raise forms.ValidationError(
                    'Passwords do not match'
                )
        
        return cleaned_data
```

---

## Built-in Validators

```python
from django.core.validators import (
    MinValueValidator,
    MaxValueValidator,
    URLValidator,
    EmailValidator,
    FileExtensionValidator
)

class MyForm(forms.Form):
    age = forms.IntegerField(
        validators=[
            MinValueValidator(0),
            MaxValueValidator(150)
        ]
    )
    
    website = forms.URLField(validators=[URLValidator()])
    
    document = forms.FileField(
        validators=[
            FileExtensionValidator(allowed_extensions=['pdf', 'doc'])
        ]
    )
```

---

# PART 5: CUSTOM VALIDATION

## Custom Validators

```python
from django.core.exceptions import ValidationError

def validate_even_number(value):
    if value % 2 != 0:
        raise ValidationError('This number is not even')

def validate_no_numbers(value):
    if any(char.isdigit() for char in value):
        raise ValidationError('This field cannot contain numbers')

class MyForm(forms.Form):
    even_number = forms.IntegerField(validators=[validate_even_number])
    text = forms.CharField(validators=[validate_no_numbers])
```

---

## Complex Validation

```python
class UserRegistrationForm(forms.Form):
    username = forms.CharField(max_length=100)
    email = forms.EmailField()
    password = forms.CharField(widget=forms.PasswordInput)
    password_confirm = forms.CharField(widget=forms.PasswordInput)
    
    def clean_username(self):
        username = self.cleaned_data['username']
        if User.objects.filter(username=username).exists():
            raise forms.ValidationError('Username already taken')
        if len(username) < 3:
            raise forms.ValidationError('Username too short')
        return username
    
    def clean_password(self):
        password = self.cleaned_data['password']
        if len(password) < 8:
            raise forms.ValidationError('Password too short')
        return password
    
    def clean(self):
        cleaned_data = super().clean()
        if cleaned_data.get('password') != cleaned_data.get('password_confirm'):
            raise forms.ValidationError('Passwords do not match')
        return cleaned_data
```

---

# PART 6: MODELFORMS

## Basic ModelForm

```python
from django.forms import ModelForm
from .models import Product

class ProductForm(ModelForm):
    class Meta:
        model = Product
        fields = ['name', 'price', 'description', 'category']
        labels = {
            'name': 'Product Name',
            'price': 'Price ($)',
        }
        help_texts = {
            'description': 'Describe your product'
        }
```

---

## ModelForm in View

```python
def create_product(request):
    if request.method == 'POST':
        form = ProductForm(request.POST, request.FILES)
        if form.is_valid():
            form.save()  # Saves to database
            return redirect('product_list')
    else:
        form = ProductForm()
    
    return render(request, 'product_form.html', {'form': form})
```

---

## Customize ModelForm

```python
class ProductForm(ModelForm):
    description = forms.CharField(
        widget=forms.Textarea(attrs={'rows': 5}),
        help_text='Enter detailed description'
    )
    
    class Meta:
        model = Product
        fields = ['name', 'price', 'description', 'category']
        widgets = {
            'name': forms.TextInput(attrs={
                'class': 'form-control',
                'placeholder': 'Product name'
            }),
            'price': forms.NumberInput(attrs={
                'class': 'form-control',
                'step': '0.01'
            }),
            'category': forms.Select(attrs={
                'class': 'form-control'
            })
        }
        labels = {
            'name': 'Product Name',
            'price': 'Price (USD)',
        }
```

---

## Edit ModelForm

```python
def edit_product(request, pk):
    product = Product.objects.get(pk=pk)
    
    if request.method == 'POST':
        form = ProductForm(request.POST, request.FILES, instance=product)
        if form.is_valid():
            form.save()
            return redirect('product_detail', pk=pk)
    else:
        form = ProductForm(instance=product)
    
    return render(request, 'product_form.html', {'form': form})
```

---

## Exclude & Include

```python
class ProductForm(ModelForm):
    class Meta:
        model = Product
        # Include specific fields
        fields = ['name', 'price', 'description']
        
        # Or exclude fields
        exclude = ['created_at', 'updated_at', 'slug']
```

---

# PART 7: FORM INHERITANCE

## Extending Forms

```python
# Base form
class BaseForm(forms.Form):
    email = forms.EmailField()
    phone = forms.CharField(max_length=20)

# Extended form
class ExtendedForm(BaseForm):
    address = forms.CharField()
    city = forms.CharField()

# Now ExtendedForm has email, phone, address, city
```

---

## Customizing Parent Form

```python
class MyForm(BaseForm):
    email = forms.EmailField(required=False)  # Override
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        # Customize parent fields
        self.fields['phone'].label = 'Contact Number'
```

---

# PART 8: FORMSETS & MODEL FORMSETS

## Basic Formset

```python
from django.forms import formset_factory

ProductFormSet = formset_factory(ProductForm, extra=3)

def edit_products(request):
    if request.method == 'POST':
        formset = ProductFormSet(request.POST, request.FILES)
        if formset.is_valid():
            for form in formset:
                if form.cleaned_data:
                    form.save()
            return redirect('products')
    else:
        formset = ProductFormSet()
    
    return render(request, 'product_formset.html', {'formset': formset})
```

---

## ModelFormSet

```python
from django.forms import modelformset_factory

ProductModelFormSet = modelformset_factory(
    Product,
    fields=['name', 'price'],
    extra=2
)

def edit_products(request):
    if request.method == 'POST':
        formset = ProductModelFormSet(request.POST)
        if formset.is_valid():
            formset.save()
            return redirect('products')
    else:
        formset = ProductModelFormSet()
    
    return render(request, 'product_formset.html', {'formset': formset})
```

---

## Formset in Template

```django
<form method="post">
    {% csrf_token %}
    {{ formset.management_form }}
    
    {% for form in formset %}
        <div class="form-group">
            {{ form.name }}
            {{ form.price }}
            {% if form.errors %}
                <ul class="errors">
                {% for error in form.non_field_errors %}
                    <li>{{ error }}</li>
                {% endfor %}
                </ul>
            {% endif %}
        </div>
    {% endfor %}
    
    <button type="submit">Save All</button>
</form>
```

---

# PART 9: AJAX FORM SUBMISSION

## AJAX POST Request

```python
def save_comment(request):
    if request.method == 'POST' and request.headers.get('x-requested-with') == 'XMLHttpRequest':
        form = CommentForm(request.POST)
        if form.is_valid():
            form.save()
            return JsonResponse({'status': 'success'})
        else:
            return JsonResponse({'status': 'error', 'errors': form.errors})
```

---

## JavaScript Handler

```javascript
document.getElementById('commentForm').addEventListener('submit', function(e) {
    e.preventDefault();
    
    const formData = new FormData(this);
    
    fetch('/save-comment/', {
        method: 'POST',
        headers: {
            'x-requested-with': 'XMLHttpRequest',
            'x-csrftoken': document.querySelector('[name=csrfmiddlewaretoken]').value
        },
        body: formData
    })
    .then(response => response.json())
    .then(data => {
        if (data.status === 'success') {
            alert('Comment saved!');
            this.reset();
        } else {
            alert('Error: ' + JSON.stringify(data.errors));
        }
    });
});
```

---

# PART 10: FORM SECURITY

## CSRF Protection

```django
<form method="post">
    {% csrf_token %}
    {# Form fields #}
    <button type="submit">Submit</button>
</form>
```

---

## File Upload Security

```python
from django.core.validators import FileExtensionValidator

class DocumentUploadForm(forms.Form):
    document = forms.FileField(
        validators=[
            FileExtensionValidator(
                allowed_extensions=['pdf', 'doc', 'docx'],
                message='Only PDF and DOC files allowed'
            )
        ]
    )
    
    def clean_document(self):
        document = self.cleaned_data['document']
        
        # Check file size
        if document.size > 5 * 1024 * 1024:  # 5MB
            raise forms.ValidationError('File too large (max 5MB)')
        
        return document
```

---

# PART 11: ERROR HANDLING & DISPLAY

## Form Errors in Template

```django
{% if form.non_field_errors %}
    <div class="alert alert-danger">
    {% for error in form.non_field_errors %}
        <p>{{ error }}</p>
    {% endfor %}
    </div>
{% endif %}

{% for field in form %}
    <div class="form-group">
        <label for="{{ field.id_for_label }}">{{ field.label }}</label>
        {{ field }}
        
        {% if field.errors %}
            <div class="alert alert-danger">
            {% for error in field.errors %}
                <p>{{ error }}</p>
            {% endfor %}
            </div>
        {% endif %}
    </div>
{% endfor %}
```

---

## Custom Error Messages

```python
class MyForm(forms.Form):
    email = forms.EmailField(
        error_messages={
            'required': 'Please enter your email',
            'invalid': 'Enter a valid email address',
        }
    )
    
    age = forms.IntegerField(
        error_messages={
            'required': 'Age is required',
            'invalid': 'Enter a valid number',
        }
    )
```

---

# PART 12: FILE UPLOAD HANDLING

## Simple File Upload

```python
class DocumentForm(forms.Form):
    file = forms.FileField()

def upload_document(request):
    if request.method == 'POST':
        form = DocumentForm(request.POST, request.FILES)
        if form.is_valid():
            uploaded_file = request.FILES['file']
            # Process file
            document = Document.objects.create(
                file=uploaded_file,
                uploaded_by=request.user
            )
            return redirect('document_detail', pk=document.pk)
    else:
        form = DocumentForm()
    
    return render(request, 'upload.html', {'form': form})
```

---

## Multiple File Upload

```python
def upload_multiple(request):
    if request.method == 'POST':
        files = request.FILES.getlist('files')
        
        for file in files:
            Document.objects.create(
                file=file,
                uploaded_by=request.user
            )
        
        return redirect('documents')
    
    return render(request, 'upload_multiple.html')
```

---

# PART 13: DYNAMIC FORMS

## Dynamic Field Generation

```python
class DynamicForm(forms.Form):
    def __init__(self, categories=None, *args, **kwargs):
        super().__init__(*args, **kwargs)
        
        if categories:
            self.fields['category'] = forms.ChoiceField(
                choices=[(c.id, c.name) for c in categories]
            )
```

---

## Usage

```python
def create_product(request):
    categories = Category.objects.all()
    
    if request.method == 'POST':
        form = DynamicForm(categories, request.POST)
    else:
        form = DynamicForm(categories)
    
    return render(request, 'form.html', {'form': form})
```

---

# PART 14: TESTING FORMS

## Form Tests

```python
from django.test import TestCase
from .forms import ProductForm

class ProductFormTest(TestCase):
    def test_valid_form(self):
        form_data = {
            'name': 'Test Product',
            'price': '9.99',
            'description': 'Test description'
        }
        form = ProductForm(data=form_data)
        self.assertTrue(form.is_valid())
    
    def test_empty_form(self):
        form = ProductForm(data={})
        self.assertFalse(form.is_valid())
    
    def test_invalid_price(self):
        form_data = {
            'name': 'Test',
            'price': 'invalid',
            'description': 'Test'
        }
        form = ProductForm(data=form_data)
        self.assertFalse(form.is_valid())
        self.assertIn('price', form.errors)
```

---

# PART 15: COMMON PATTERNS

## Bootstrap Form Rendering

```django
{% for field in form %}
    <div class="form-group mb-3">
        <label class="form-label" for="{{ field.id_for_label }}">
            {{ field.label }}
        </label>
        {{ field }}
        {% if field.errors %}
            <div class="invalid-feedback d-block">
                {% for error in field.errors %}
                    {{ error }}
                {% endfor %}
            </div>
        {% endif %}
    </div>
{% endfor %}
```

---

## Search Form

```python
class SearchForm(forms.Form):
    q = forms.CharField(
        required=False,
        widget=forms.TextInput(attrs={
            'class': 'form-control',
            'placeholder': 'Search...',
        })
    )
    category = forms.ModelChoiceField(
        queryset=Category.objects.all(),
        required=False,
        empty_label='All Categories'
    )

def search_view(request):
    form = SearchForm(request.GET or None)
    results = Product.objects.all()
    
    if form.is_valid():
        q = form.cleaned_data.get('q')
        category = form.cleaned_data.get('category')
        
        if q:
            results = results.filter(name__icontains=q)
        if category:
            results = results.filter(category=category)
    
    return render(request, 'search.html', {
        'form': form,
        'results': results
    })
```

---

# PART 16: 50+ PRACTICAL EXAMPLES

## Example 1: Contact Form

```python
from django import forms
from django.core.mail import send_mail

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    subject = forms.CharField(max_length=200)
    message = forms.CharField(widget=forms.Textarea)

def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            send_mail(
                form.cleaned_data['subject'],
                form.cleaned_data['message'],
                form.cleaned_data['email'],
                ['admin@example.com'],
            )
            return redirect('contact_success')
    else:
        form = ContactForm()
    
    return render(request, 'contact.html', {'form': form})
```

---

## Example 2: Registration Form

```python
from django.contrib.auth.models import User

class RegistrationForm(forms.Form):
    username = forms.CharField(max_length=100)
    email = forms.EmailField()
    password = forms.CharField(widget=forms.PasswordInput)
    password_confirm = forms.CharField(widget=forms.PasswordInput)
    
    def clean_username(self):
        username = self.cleaned_data['username']
        if User.objects.filter(username=username).exists():
            raise forms.ValidationError('Username taken')
        return username
    
    def clean(self):
        cleaned_data = super().clean()
        if cleaned_data.get('password') != cleaned_data.get('password_confirm'):
            raise forms.ValidationError('Passwords do not match')
        return cleaned_data
```

---

# PART 17: INTERVIEW Q&A

**Q: What's the difference between Form and ModelForm?**
A: Form is manual field definition. ModelForm auto-generates fields from a Model.

**Q: How does form validation work?**
A: is_valid() calls clean_<fieldname>() for each field, then clean() for form-level validation.

**Q: What's cleaned_data?**
A: A dictionary containing validated, converted data after is_valid() passes.

**Q: How do you secure file uploads?**
A: Validate file type, size, and scan for malicious content.

---

# PART 18: QUICK REFERENCE

| Method | Purpose |
|--------|---------|
| `is_valid()` | Validate form |
| `cleaned_data` | Validated data |
| `errors` | Form errors |
| `save()` | Save ModelForm to DB |
| `clean_<field>()` | Field validation |
| `clean()` | Form validation |

---

**Master forms and build interactive web applications!** 📋
