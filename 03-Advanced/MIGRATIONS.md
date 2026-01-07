# 🔄 DJANGO MIGRATIONS - COMPLETE TUTORIAL A-Z

**Master Database Migrations, Schema Changes, and Data Migrations**

---

## 📖 TABLE OF CONTENTS

1. Migration Fundamentals
2. Creating Migrations
3. Applying Migrations
4. Data Migrations
5. Reversing Migrations
6. Squashing Migrations
7. Handling Conflicts
8. Complex Schema Changes
9. Migration Best Practices
10. Performance Optimization
11. Testing Migrations
12. Production Migrations
13. Troubleshooting
14. Advanced Techniques
15. 30+ Practical Examples
16. Interview Q&A
17. Quick Reference

---

# PART 1: MIGRATION FUNDAMENTALS

## What are Migrations?

Migrations are version-controlled database schema changes. They track model changes and update the database.

### Architecture

```
Model Changes → makemigrations → Migration File → migrate → Database
```

---

## Migration Files

```python
# 0001_initial.py
from django.db import migrations, models

class Migration(migrations.Migration):
    initial = True
    
    dependencies = []
    
    operations = [
        migrations.CreateModel(
            name='Product',
            fields=[
                ('id', models.AutoField(primary_key=True)),
                ('name', models.CharField(max_length=100)),
            ],
        ),
    ]
```

---

## Migration Commands

```bash
# Create new migrations
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# See migration status
python manage.py showmigrations

# Unapply migrations
python manage.py migrate app_name zero

# Squash migrations
python manage.py squashmigrations app_name 0001
```

---

# PART 2: CREATING MIGRATIONS

## Auto-generate Migrations

```python
# models.py
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    description = models.TextField()

# Run: python manage.py makemigrations
# Creates: migrations/0001_initial.py
```

---

## Named Migrations

```bash
# Create migration with specific name
python manage.py makemigrations app_name --name add_product_fields

# Creates: migrations/0002_add_product_fields.py
```

---

## Empty Migration

```bash
# Create empty migration for data changes
python manage.py makemigrations app_name --empty --name populate_products

# Creates: migrations/0003_populate_products.py
```

---

## Multiple App Migrations

```bash
# Migrate specific app
python manage.py migrate app_name

# Migrate all apps
python manage.py migrate
```

---

# PART 3: APPLYING MIGRATIONS

## Apply Migrations

```bash
# Apply all pending migrations
python manage.py migrate

# Apply specific migration
python manage.py migrate app_name 0001

# Apply specific app
python manage.py migrate app_name

# Apply up to specific migration
python manage.py migrate app_name 0003
```

---

## View Migration Status

```bash
# Show migration status
python manage.py showmigrations

# Output:
# app_name
#  [X] 0001_initial
#  [X] 0002_add_fields
#  [ ] 0003_new_feature
```

---

## Plan Migrations

```bash
# Plan migrations without applying
python manage.py migrate --plan

# See SQL that will be executed
python manage.py sqlmigrate app_name 0001
```

---

# PART 4: DATA MIGRATIONS

## Create Data Migration

```bash
python manage.py makemigrations --empty myapp --name populate_data
```

---

## Populate Data

```python
# migrations/0003_populate_data.py
from django.db import migrations

def populate_categories(apps, schema_editor):
    Category = apps.get_model('myapp', 'Category')
    
    categories = [
        Category(name='Electronics'),
        Category(name='Books'),
        Category(name='Clothing'),
    ]
    
    Category.objects.bulk_create(categories)

def reverse_populate(apps, schema_editor):
    Category = apps.get_model('myapp', 'Category')
    Category.objects.all().delete()

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0002_category'),
    ]
    
    operations = [
        migrations.RunPython(populate_categories, reverse_populate),
    ]
```

---

## Run Raw SQL

```python
# migrations/0004_raw_sql.py
from django.db import migrations

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0003_populate_data'),
    ]
    
    operations = [
        migrations.RunSQL(
            "UPDATE myapp_product SET featured=1 WHERE price > 100",
            "UPDATE myapp_product SET featured=0 WHERE price > 100"
        ),
    ]
```

---

# PART 5: REVERSING MIGRATIONS

## Unapply Migrations

```bash
# Go back to zero state
python manage.py migrate app_name zero

# Go back one migration
python manage.py migrate app_name 0001

# Go back to specific migration
python manage.py migrate app_name 0002
```

---

## Reversible Migrations

```python
# migrations/0002_add_discount.py
from django.db import migrations, models

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0001_initial'),
    ]
    
    operations = [
        migrations.AddField(
            model_name='product',
            name='discount',
            field=models.DecimalField(
                max_digits=5,
                decimal_places=2,
                default=0
            ),
        ),
    ]
    
    # This can be reversed automatically
```

---

# PART 6: SQUASHING MIGRATIONS

## Squash Old Migrations

```bash
# Squash migrations 0001 through 0005
python manage.py squashmigrations app_name 0001 0005

# Creates: migrations/0001_squashed_0005_description.py
```

---

## After Squashing

```bash
# Delete old migration files (keep squashed)
rm migrations/0001_initial.py
rm migrations/0002_add_fields.py
# ... etc

# Squashed migration replaces them all
# New migrations depend on squashed migration
```

---

# PART 7: HANDLING CONFLICTS

## Merge Migrations

```bash
# When multiple migrations created for same model
python manage.py makemigrations --merge

# Creates: migrations/0003_merge_0002_0002.py
```

---

## Manual Merge Resolution

```python
# migrations/0003_merge.py
from django.db import migrations

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0002_auto_branch1'),
        ('myapp', '0002_auto_branch2'),
    ]
    
    operations = [
        # Resolve conflicts here
    ]
```

---

# PART 8: COMPLEX SCHEMA CHANGES

## Rename Field

```python
# migrations/0002_rename_field.py
from django.db import migrations, models

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0001_initial'),
    ]
    
    operations = [
        migrations.RenameField(
            model_name='product',
            old_name='description_short',
            new_name='short_description',
        ),
    ]
```

---

## Rename Model

```python
class Migration(migrations.Migration):
    operations = [
        migrations.RenameModel(
            old_name='OldModel',
            new_name='NewModel',
        ),
    ]
```

---

## Change Field Type

```python
class Migration(migrations.Migration):
    operations = [
        migrations.AlterField(
            model_name='product',
            name='rating',
            field=models.DecimalField(
                max_digits=3,
                decimal_places=2,
                default=0
            ),
        ),
    ]
```

---

## Add Unique Constraint

```python
class Migration(migrations.Migration):
    operations = [
        migrations.AlterUniqueTogether(
            name='product',
            unique_together={('category', 'name')},
        ),
    ]
```

---

# PART 9: MIGRATION BEST PRACTICES

## Golden Rules

```
1. Commit migrations with code
2. Never edit applied migrations
3. Create new migration for changes
4. Test migrations before production
5. Always write reversible migrations
6. Keep migrations atomic
```

---

## Good Practice

```python
# ✅ Good: Specific, reversible, atomic
class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0001_initial'),
    ]
    
    operations = [
        migrations.AddField(
            model_name='product',
            name='sku',
            field=models.CharField(max_length=50),
        ),
    ]

# ❌ Bad: Non-reversible, unclear
class Migration(migrations.Migration):
    operations = [
        migrations.RunSQL(
            "ALTER TABLE myapp_product ADD COLUMN sku VARCHAR(50)",
        ),
    ]
```

---

# PART 10: PERFORMANCE OPTIMIZATION

## Large Table Migrations

```python
# For large tables, add index separately
class Migration(migrations.Migration):
    operations = [
        migrations.AddField(
            model_name='product',
            name='search_text',
            field=models.TextField(default=''),
            preserve_default=False,
        ),
        migrations.RunSQL(
            "CREATE INDEX idx_search_text ON myapp_product(search_text)",
            "DROP INDEX idx_search_text"
        ),
    ]
```

---

## Batch Operations

```python
# Use batch_size for large migrations
def populate_data(apps, schema_editor):
    Product = apps.get_model('myapp', 'Product')
    
    products = []
    for i in range(100000):
        products.append(Product(name=f'Product {i}'))
    
    Product.objects.bulk_create(products, batch_size=1000)
```

---

# PART 11: TESTING MIGRATIONS

## Test Migrations

```python
from django.test import TestCase
from django.core.management import call_command
from io import StringIO

class MigrationTestCase(TestCase):
    def test_migration_forward(self):
        call_command('migrate', 'myapp', '0001')
        
        # Test migration result
        from myapp.models import Product
        self.assertEqual(Product.objects.count(), 0)
    
    def test_migration_reverse(self):
        call_command('migrate', 'myapp', '0001')
        call_command('migrate', 'myapp', 'zero')
        
        # Verify database state
```

---

# PART 12: PRODUCTION MIGRATIONS

## Safe Production Migrations

```bash
# 1. Backup database
# 2. Test migrations in staging
# 3. Plan migration window

# 4. Check migrations to apply
python manage.py showmigrations --plan

# 5. Apply migrations
python manage.py migrate --verbosity 2

# 6. Verify application
```

---

## No-Downtime Migrations

```python
# 1. Add new field (nullable or with default)
class Migration(migrations.Migration):
    operations = [
        migrations.AddField(
            model_name='product',
            name='new_field',
            field=models.CharField(max_length=100, null=True),
        ),
    ]

# 2. Update application code
# 3. Populate new field
# 4. Remove old field
```

---

# PART 13: TROUBLESHOOTING

## Common Issues

### Migration Not Found
```bash
# Ensure migrations directory exists
mkdir -p myapp/migrations

# Create __init__.py
touch myapp/migrations/__init__.py
```

### Conflicting Migrations
```bash
# View conflicts
python manage.py migrate --list

# Merge conflicts
python manage.py makemigrations --merge
```

### Can't Reverse Migration
```python
# Provide reverse operation
class Migration(migrations.Migration):
    operations = [
        migrations.RunSQL(
            forward_sql,
            reverse_sql  # Required for reversibility
        ),
    ]
```

---

# PART 14: ADVANCED TECHNIQUES

## Custom Migration Operations

```python
# migrations.py
from django.db.migrations.operations.base import Operation

class MyCustomOperation(Operation):
    def state_forwards(self, app_label, state):
        # Modify state
        return state
    
    def database_forwards(self, app_label, schema_editor, from_state, to_state):
        # Execute database changes
        pass
    
    def database_backwards(self, app_label, schema_editor, from_state, to_state):
        # Reverse changes
        pass

# Usage
class Migration(migrations.Migration):
    operations = [
        MyCustomOperation(),
    ]
```

---

# PART 15: 30+ PRACTICAL EXAMPLES

## Example: Add Field with Default

```python
from django.db import migrations, models

class Migration(migrations.Migration):
    dependencies = [
        ('myapp', '0001_initial'),
    ]
    
    operations = [
        migrations.AddField(
            model_name='product',
            name='is_featured',
            field=models.BooleanField(default=False),
            preserve_default=False,
        ),
    ]
```

---

# PART 16: INTERVIEW Q&A

**Q: Why do we need migrations?**
A: To version-control database changes and keep dev/staging/prod in sync.

**Q: Can you edit applied migrations?**
A: No, create a new migration instead.

**Q: How do you squash migrations?**
A: Use `python manage.py squashmigrations` to combine multiple migrations.

---

# PART 17: QUICK REFERENCE

| Command | Purpose |
|---------|---------|
| `makemigrations` | Create migration files |
| `migrate` | Apply migrations |
| `showmigrations` | View migration status |
| `sqlmigrate` | See SQL |
| `squashmigrations` | Combine migrations |
| `migrate app zero` | Unapply all |

---

**Master migrations and maintain database consistency!** 🔄
