# 🐍 Python OOP — Complete & Detailed Notes (A to Z)

> **Before We Start — What is OOP?**
>
> OOP stands for **Object-Oriented Programming**. It's a way of writing code where you
> organize everything around *objects* — just like the real world is full of objects
> (phones, cars, people, orders on a website).
>
> Instead of writing a long list of instructions, you create "blueprints" (classes)
> and then make actual things (objects) from them. This makes your code cleaner,
> reusable, and much easier to manage in big projects.

---

## 📌 Table of Contents

1. [Class Fundamentals](#31-class-fundamentals) — Class, Object, Attributes, Methods
2. [Constructor](#32-constructor----__init__) — `__init__()`
3. [Inheritance](#33-inheritance) — Single, Multiple, Multilevel
4. [OOP Principles](#34-oop-principles) — Polymorphism, Encapsulation, Abstraction
5. [Method Types](#35-method-types) — Instance, Class, Static
6. [Special Methods](#36-special-dunder-methods) — `__str__`, `__repr__`, `__len__`
7. [super() and MRO](#37-super-and-mro) — `super()`, Method Resolution Order

---

---

# 3.1 Class Fundamentals

## 🔵 What is a Class?

### Plain English Explanation

A **class** is a **blueprint** or a **template**. It is NOT the actual thing — it's just
the *plan* for making the thing.

### 🧒 Explain Like I'm a Child

Imagine you want to make 100 toy robots. Do you build each robot from scratch?
No! You first make a **blueprint** (the design on paper). Then you use that same
blueprint to build Robot 1, Robot 2, Robot 3... and so on.

- The **blueprint** = Class
- Each **actual robot** you build from it = Object

Every robot has the same design (same class), but each one can have different
colors, names, or battery levels (different attribute values).

### Syntax

```python
class ClassName:
    # attributes and methods go here
    pass
```

### Simple Example

```python
class Robot:        # This is the blueprint
    pass            # Empty for now

r1 = Robot()        # Build robot 1
r2 = Robot()        # Build robot 2

print(type(r1))     # <class '__main__.Robot'>
print(r1 == r2)     # False — two DIFFERENT objects, even from same class
```

### 🌐 Real Website Example — Amazon

Amazon's entire product catalog is built using classes. Every product listed on
Amazon (phone, book, headphones) is an **object** created from a `Product` class.

```python
# How Amazon might define a Product internally
class Product:
    pass

# Each product listed on the site is a separate object
iphone    = Product()
headphones = Product()
notebook   = Product()

# Millions of product objects — all from ONE class blueprint
```

---

## 🔵 What is an Object?

### Plain English Explanation

An **object** (also called an **instance**) is a **real thing** created from a class.
When you use a class to create something, that "something" is an object.
Each object is independent — changing one doesn't affect another.

### 🧒 Explain Like I'm a Child

Think of an iPhone. Apple has one **design** (class). But millions of iPhones
(objects) are made from that same design. Your iPhone has YOUR photos, YOUR apps,
YOUR name. Your friend's iPhone has their stuff. Same blueprint, different data.

### Example

```python
class Student:
    pass

# Creating three separate objects from the same class
student1 = Student()
student2 = Student()
student3 = Student()

# Each is a unique, independent object in memory
print(id(student1))   # e.g., 140234567
print(id(student2))   # e.g., 140234891  ← different memory address
```

### 🌐 Real Website Example — Facebook / Instagram

Every user account on Facebook is an **object** created from a `User` class.
There are 3 billion user objects — but only ONE User class.

```python
class User:
    pass

user_awais   = User()   # Awais's account object
user_ali     = User()   # Ali's account object
user_sara    = User()   # Sara's account object

# Billions of objects — all from the same User class
```

---

## 🔵 What are Attributes?

### Plain English Explanation

**Attributes** are **variables that belong to an object**. They store information
*about* the object — they describe what the object *is* or *has*.

There are two types:

| Type | Belongs To | Shared? | Defined Where |
|---|---|---|---|
| **Class Attribute** | The CLASS itself | Yes — ALL objects share it | Outside `__init__` |
| **Instance Attribute** | Each individual OBJECT | No — each object has its own | Inside `__init__` |

### 🧒 Explain Like I'm a Child

Think of students in a school:
- Every student goes to the **same school** → that's a **class attribute** (shared by all)
- But every student has their **own name and grade** → those are **instance attributes** (unique to each)

### Example

```python
class Student:
    school = "UET Lahore"       # Class attribute — SAME for all students

    def __init__(self, name, grade):
        self.name  = name       # Instance attribute — DIFFERENT per student
        self.grade = grade      # Instance attribute — DIFFERENT per student

s1 = Student("Awais", "A")
s2 = Student("Ali",   "B")

# Instance attributes — unique per object
print(s1.name)    # Awais
print(s2.name)    # Ali

# Class attribute — same for everyone
print(s1.school)  # UET Lahore
print(s2.school)  # UET Lahore

# Changing class attribute affects ALL objects
Student.school = "FAST University"
print(s1.school)  # FAST University  ← changed!
print(s2.school)  # FAST University  ← changed for everyone!
```

### 🌐 Real Website Example — YouTube

Every YouTube video has its own attributes. The YouTube backend has a `Video` class
and creates one object per video uploaded. Each video object stores its own data:

```python
class Video:
    platform = "YouTube"          # Class attribute — same for all videos

    def __init__(self, title, views, likes, channel):
        self.title   = title       # Instance attribute
        self.views   = views       # Instance attribute
        self.likes   = likes       # Instance attribute
        self.channel = channel     # Instance attribute

# Two different video objects on YouTube
v1 = Video("Python Tutorial", 1_500_000, 45_000, "Corey Schafer")
v2 = Video("OOP Explained",     300_000, 12_000, "Tech With Tim")

print(v1.title)    # Python Tutorial
print(v2.views)    # 300000
print(v1.platform) # YouTube  ← from class attribute
```

---

## 🔵 What are Methods?

### Plain English Explanation

**Methods** are **functions defined inside a class**. They define the *actions* or
*behaviors* that an object can perform. While attributes describe what an object
*is* (its data), methods describe what it *can do* (its actions).

Every method in a class receives **`self`** as the first parameter — `self` is
Python's way of saying "this specific object right here."

### 🧒 Explain Like I'm a Child

A robot (object) has properties like color and height (attributes). But it can
also *do things* — walk, talk, charge itself. Those actions are **methods**.

When you tell Robot-1 to walk, only Robot-1 walks, not all robots. That's what
`self` is for — it makes sure the method runs on *that specific robot*.

### Example

```python
class Robot:
    def __init__(self, name, battery):
        self.name    = name
        self.battery = battery

    def greet(self):                              # Method — takes no extra input
        print(f"Hi! I am {self.name}.")

    def charge(self, amount):                     # Method — takes extra input
        self.battery += amount
        print(f"{self.name} charged! Battery: {self.battery}%")

    def status(self):
        print(f"{self.name} | Battery: {self.battery}%")

r1 = Robot("R2D2", 50)
r2 = Robot("C3PO", 20)

r1.greet()          # Hi! I am R2D2.
r2.greet()          # Hi! I am C3PO.
r1.charge(30)       # R2D2 charged! Battery: 80%
r2.status()         # C3PO | Battery: 20%   ← r2 is unchanged
```

### 🌐 Real Website Example — Twitter / X

When you click "Like" or "Retweet" on Twitter, those are **methods** being called
on a Tweet object:

```python
class Tweet:
    def __init__(self, author, content):
        self.author   = author
        self.content  = content
        self.likes    = 0
        self.retweets = 0

    def like(self):                               # Called when you click ❤️
        self.likes += 1
        print(f"Tweet liked! Total likes: {self.likes}")

    def retweet(self):                            # Called when you click 🔁
        self.retweets += 1
        print(f"Retweeted! Total retweets: {self.retweets}")

    def display(self):
        print(f"@{self.author}: {self.content}")
        print(f"❤️ {self.likes}  🔁 {self.retweets}")

t = Tweet("awais_dev", "Just learned Python OOP! 🐍")
t.like()
t.like()
t.retweet()
t.display()
# @awais_dev: Just learned Python OOP! 🐍
# ❤️ 2  🔁 1
```

---

### 📊 Summary Table — Class Fundamentals

| Concept | What It Is | Real Life | Python Syntax |
|---|---|---|---|
| **Class** | Blueprint / Template | iPhone design at Apple | `class iPhone:` |
| **Object** | Actual created thing | Your specific iPhone | `my_phone = iPhone()` |
| **Class Attribute** | Data shared by ALL objects | School name for all students | `school = "UET"` |
| **Instance Attribute** | Data unique to ONE object | Your specific name/grade | `self.name = name` |
| **Method** | Action the object can do | Click Like on a Tweet | `def like(self):` |

---

---

# 3.2 Constructor — `__init__()`

## Plain English Explanation

A **constructor** is a special method that runs **automatically** the moment
you create a new object. In Python, the constructor is always named `__init__()`.

You use the constructor to **set up** the object — give it its starting data,
initial values, or anything it needs right from birth.

Think of it like filling out a form when you sign up for a new account. The moment
you hit "Register," the system automatically takes your name, email, and password
and sets them up for your profile. That automatic setup = constructor.

## 🧒 Explain Like I'm a Child

When a new baby is born, some things happen automatically:
- They get a name
- They get a birth date
- They get registered at the hospital

You don't have to manually do each step *after* the birth — it all happens
automatically when the baby arrives. That's exactly what `__init__()` does:
it sets everything up automatically the moment an object is "born."

## Syntax

```python
class ClassName:
    def __init__(self, param1, param2):
        self.attribute1 = param1
        self.attribute2 = param2
```

## Understanding `self`

`self` is a reference to **the current object being created**. It's how Python
knows which object's attributes to set. Without `self`, Python wouldn't know
whether you mean Robot-1's battery or Robot-2's battery.

```
self = "the object I'm currently working with"
```

## Detailed Example — Step by Step

```python
class BankAccount:
    def __init__(self, owner, account_number, initial_balance=0):
        # These lines run AUTOMATICALLY when you do BankAccount(...)
        self.owner          = owner
        self.account_number = account_number
        self.balance        = initial_balance
        self.is_active      = True          # Default value
        print(f"✅ Account created for {self.owner}!")

    def show_info(self):
        print(f"Owner:   {self.owner}")
        print(f"Acc No:  {self.account_number}")
        print(f"Balance: PKR {self.balance:,}")
        print(f"Active:  {self.is_active}")

# The moment these lines run, __init__ is called automatically
acc1 = BankAccount("Awais", "PK-001", 50000)
# Output: ✅ Account created for Awais!

acc2 = BankAccount("Ali", "PK-002")
# Output: ✅ Account created for Ali!
# (balance defaults to 0 because we set initial_balance=0)

acc1.show_info()
# Owner:   Awais
# Acc No:  PK-001
# Balance: PKR 50,000
# Active:  True
```

## Default Values in Constructor

You can set default values for parameters — these are used when the caller
doesn't provide that argument:

```python
class UserProfile:
    def __init__(self, username, email, role="viewer", is_verified=False):
        self.username    = username
        self.email       = email
        self.role        = role          # Defaults to "viewer"
        self.is_verified = is_verified   # Defaults to False

u1 = UserProfile("awais", "awais@gmail.com")              # uses defaults
u2 = UserProfile("admin", "admin@site.com", "admin", True) # overrides defaults

print(u1.role)        # viewer
print(u2.role)        # admin
print(u1.is_verified) # False
print(u2.is_verified) # True
```

## 🌐 Real Website Example — Daraz / Shopify (E-Commerce)

When a customer places a new order on Daraz, the system creates a new `Order` object.
The constructor automatically timestamps it, assigns an order ID, and sets status to "pending":

```python
import datetime

class Order:
    order_counter = 1000    # Class attribute — shared, keeps track of last ID

    def __init__(self, customer_name, items, address):
        # Auto-setup when order is placed
        Order.order_counter += 1
        self.order_id      = f"ORD-{Order.order_counter}"
        self.customer      = customer_name
        self.items         = items
        self.address       = address
        self.status        = "Pending"
        self.placed_at     = datetime.datetime.now()
        print(f"🛒 Order {self.order_id} placed successfully!")

    def update_status(self, new_status):
        self.status = new_status
        print(f"📦 Order {self.order_id} is now: {self.status}")

# Customer places orders — constructor runs automatically each time
order1 = Order("Awais", ["Python Book", "USB Hub"], "Lahore, Pakistan")
order2 = Order("Sara",  ["Headphones"],              "Karachi, Pakistan")

order1.update_status("Shipped")
order2.update_status("Out for Delivery")
```

---

---

# 3.3 Inheritance

## Plain English Explanation

**Inheritance** allows one class (child) to acquire all the properties and methods
of another class (parent). The child class gets everything the parent has — for
free — and can also add its own new features or change (override) existing ones.

This is one of the most powerful ideas in OOP because it means you never have to
rewrite code. Build something once in a parent class, and let all child classes
inherit and reuse it.

## 🧒 Explain Like I'm a Child

You (the child) inherited things from your parents:
- Their eye color, height, face shape → you didn't choose these, you just got them
- But you also have your own personality, skills, and hobbies that your parents don't have

That's exactly how class inheritance works:
- **Parent class** = your parents (has existing features)
- **Child class** = you (gets parent's features + adds your own)

## Types of Inheritance at a Glance

```
┌──────────────────────────────────────────────────────────────────┐
│  SINGLE          MULTIPLE          MULTILEVEL                    │
│                                                                  │
│   Parent          ParentA  ParentB   Grandparent                 │
│     │                 \   /               │                      │
│   Child               Child            Parent                   │
│                                           │                      │
│                                         Child                    │
└──────────────────────────────────────────────────────────────────┘
```

---

## 🔵 Single Inheritance — One Parent

### Explanation

One parent class, one child class. The child inherits everything from the parent
and can also define its own unique methods.

### 🧒 Like a Child

A `Vehicle` class has basic features (start, stop). A `Car` class inherits from
`Vehicle` and adds its own feature (air conditioning). Car gets start/stop for
free — no rewriting needed.

### Example

```python
class Vehicle:                          # PARENT class
    def __init__(self, brand, speed):
        self.brand = brand
        self.speed = speed

    def start(self):
        print(f"{self.brand} engine started 🚗")

    def stop(self):
        print(f"{self.brand} engine stopped ⛔")

    def info(self):
        print(f"Brand: {self.brand} | Top Speed: {self.speed} km/h")


class Car(Vehicle):                     # CHILD class — inherits Vehicle
    def __init__(self, brand, speed, num_doors):
        super().__init__(brand, speed)  # Call parent's __init__
        self.num_doors = num_doors      # Extra attribute for Car only

    def honk(self):                     # Extra method — Car only
        print(f"{self.brand} goes: Beep beep! 📯")


# Car gets ALL of Vehicle's methods for free
my_car = Car("Toyota", 180, 4)
my_car.start()     # ✅ Inherited from Vehicle → Toyota engine started 🚗
my_car.stop()      # ✅ Inherited from Vehicle → Toyota engine stopped ⛔
my_car.info()      # ✅ Inherited from Vehicle → Brand: Toyota | Top Speed: 180 km/h
my_car.honk()      # ✅ Car's own method      → Toyota goes: Beep beep! 📯
print(my_car.num_doors)  # 4 — Car's own attribute
```

### 🌐 Real Website Example — Django Web Framework (used by Instagram, Disqus)

Django, the web framework used to build Instagram, uses single inheritance for
its views. Every page/endpoint in Django inherits from a base `View` class:

```python
# Django internally has something like this:
class View:                             # Django's base View (parent)
    def get(self, request):
        pass

    def post(self, request):
        pass

    def dispatch(self, request):
        # Decides whether to call get() or post()
        if request.method == "GET":
            return self.get(request)
        else:
            return self.post(request)


# Your code inherits from it (child class):
class ProfilePage(View):                # Child — gets dispatch() for free
    def get(self, request):             # Override parent's get()
        user = get_user(request)
        return render_profile(user)

class LoginPage(View):                  # Another child
    def post(self, request):
        check_credentials(request)
        return redirect_to_home()
```

---

## 🔵 Multiple Inheritance — Two or More Parents

### Explanation

A child class inherits from **two or more parent classes** at the same time. The
child gets all features from all its parents combined.

### 🧒 Like a Child

Imagine a **Flying Fish**. It can fly (inherited from Bird family traits) AND swim
(inherited from Fish family traits). It has abilities from BOTH parents.

### Example

```python
class EmailMixin:                           # Parent 1
    def send_email(self, recipient, msg):
        print(f"📧 Email sent to {recipient}: {msg}")

class SMSMixin:                             # Parent 2
    def send_sms(self, number, msg):
        print(f"📱 SMS sent to {number}: {msg}")

class PushMixin:                            # Parent 3
    def send_push(self, user_id, msg):
        print(f"🔔 Push notification to {user_id}: {msg}")


class NotificationService(EmailMixin, SMSMixin, PushMixin):  # Inherits ALL THREE
    def notify_all(self, user, message):
        self.send_email(user["email"], message)    # From EmailMixin
        self.send_sms(user["phone"], message)      # From SMSMixin
        self.send_push(user["id"], message)        # From PushMixin


notifier = NotificationService()
user = {"email": "awais@gmail.com", "phone": "+92300", "id": "USR-42"}
notifier.notify_all(user, "Your order has been shipped!")
# 📧 Email sent to awais@gmail.com: Your order has been shipped!
# 📱 SMS sent to +92300: Your order has been shipped!
# 🔔 Push notification to USR-42: Your order has been shipped!
```

### 🌐 Real Website Example — Airbnb / Booking.com

Listing platforms combine multiple mixins for their listing pages. A listing
inherits pricing features, review features, and availability features from
separate classes:

```python
class PricingMixin:
    def get_price(self, nights):
        return self.price_per_night * nights

class ReviewMixin:
    def add_review(self, rating, comment):
        self.reviews.append({"rating": rating, "comment": comment})

    def average_rating(self):
        if not self.reviews:
            return 0
        return sum(r["rating"] for r in self.reviews) / len(self.reviews)

class AvailabilityMixin:
    def is_available(self, check_in, check_out):
        # Check against booked dates
        return True  # Simplified


class HotelListing(PricingMixin, ReviewMixin, AvailabilityMixin):
    def __init__(self, name, price_per_night):
        self.name            = name
        self.price_per_night = price_per_night
        self.reviews         = []


listing = HotelListing("Lahore Luxury Apartment", 5000)

# Uses methods from all three parent mixins:
print(listing.get_price(3))         # 15000  — from PricingMixin
listing.add_review(5, "Excellent!") # from ReviewMixin
print(listing.average_rating())     # 5.0    — from ReviewMixin
print(listing.is_available("2025-06-01", "2025-06-04"))  # from AvailabilityMixin
```

---

## 🔵 Multilevel Inheritance — Grandparent → Parent → Child

### Explanation

A chain of inheritance: Class C inherits from Class B, which itself inherits from
Class A. So C gets features from both B and A (the whole chain).

### 🧒 Like a Child

- **Grandparent** (Dada Jaan) knows how to farm
- **Parent** (your father) knows how to farm (from grandpa) + learned how to drive
- **You** know how to farm + drive (both inherited) + you know how to code (your own skill)

Each generation inherits everything above it, and adds something new.

### Example

```python
class LivingBeing:                          # Level 1 — Great ancestor
    def breathe(self):
        print("Inhale... Exhale... 💨")

    def eat(self):
        print("Eating food 🍽️")


class Animal(LivingBeing):                  # Level 2 — inherits LivingBeing
    def move(self):
        print("Moving around 🏃")

    def sleep(self):
        print("Sleeping... 💤")


class Dog(Animal):                          # Level 3 — inherits Animal → LivingBeing
    def bark(self):
        print("Woof! Woof! 🐶")

    def fetch(self):
        print("Fetching the ball! 🎾")


class GoldenRetriever(Dog):                 # Level 4 — inherits EVERYTHING above
    def swim(self):
        print("Splashing in the water! 🌊")


gr = GoldenRetriever()
gr.breathe()   # From LivingBeing (Level 1)
gr.eat()       # From LivingBeing (Level 1)
gr.move()      # From Animal     (Level 2)
gr.sleep()     # From Animal     (Level 2)
gr.bark()      # From Dog        (Level 3)
gr.fetch()     # From Dog        (Level 3)
gr.swim()      # From GoldenRetriever (own)
```

### 🌐 Real Website Example — Django's Class-Based Views (CBV)

Django's view system is a perfect real-world example of multilevel inheritance:

```python
# Level 1 — Base View (Django built-in)
class View:
    def dispatch(self, request):
        pass

# Level 2 — TemplateView inherits View (adds HTML rendering)
class TemplateResponseMixin(View):
    template_name = None
    def render_to_response(self, context):
        return render(self.request, self.template_name, context)

# Level 3 — ListView inherits TemplateView (adds list of objects)
class ListView(TemplateResponseMixin):
    model = None
    def get_queryset(self):
        return self.model.objects.all()

# Your code — Level 4 — ProductListView inherits everything
class ProductListView(ListView):    # Gets dispatch, render, queryset — all free!
    model = Product
    template_name = "products/list.html"
    # Just specify WHAT, not HOW — all the "how" is inherited
```

---

### 📊 Inheritance Comparison Table

| Feature | Single | Multiple | Multilevel |
|---|---|---|---|
| **Parents** | 1 | 2 or more | 1 (but forms a chain) |
| **Structure** | A → B | A + B → C | A → B → C |
| **Use Case** | Specialization | Combining abilities | Gradual feature layering |
| **Real Example** | `Car(Vehicle)` | `Duck(Flyable, Swimmable)` | `GoldenRetriever(Dog(Animal))` |

---

---

# 3.4 OOP Principles

## Overview

These are the **four pillars** of Object-Oriented Programming. Every major OOP
language (Python, Java, C++) is built around these ideas.

| Pillar | Core Idea | One-Line Summary |
|---|---|---|
| **Encapsulation** | Bundle data + restrict access | Hide internals, expose only what's needed |
| **Abstraction** | Hide complexity | Show WHAT, hide HOW |
| **Inheritance** | Reuse from parent | Already covered above ↑ |
| **Polymorphism** | One interface, many forms | Same method name, different behavior |

---

## 🔵 Encapsulation — "Keep It Safe Inside"

### Plain English Explanation

**Encapsulation** means bundling data (attributes) and methods together inside
a class, AND controlling who can access or modify that data.

It's about **protection**. You don't want just anyone to change your bank balance
directly. They have to go through the proper channel (a method) to do so. That
method can validate the request before making changes.

### 🧒 Explain Like I'm a Child

Think about your school's marks system. Your grade is stored in the school's
computer. Can you just walk in and change your marks? No! You have to go through
the teacher. The teacher (method) checks if the change is valid before updating
your marks (data). That protection system = Encapsulation.

### Access Levels in Python

| Prefix | Name | Accessible From | Example |
|---|---|---|---|
| No prefix | Public | Anywhere | `self.name` |
| `_` (single) | Protected | Class + subclasses (convention) | `self._salary` |
| `__` (double) | Private | Class only (enforced by Python) | `self.__password` |

### Detailed Example

```python
class UserAccount:
    def __init__(self, username, password, balance):
        self.username  = username       # PUBLIC  — anyone can read
        self._email    = None           # PROTECTED — subclasses can use
        self.__password = password      # PRIVATE  — class only
        self.__balance  = balance       # PRIVATE  — class only

    # Getter — controlled READ access to private data
    def get_balance(self):
        return self.__balance

    # Setter — controlled WRITE access with validation
    def deposit(self, amount):
        if amount <= 0:
            print("❌ Deposit amount must be positive!")
            return
        self.__balance += amount
        print(f"✅ Deposited PKR {amount:,}. New balance: PKR {self.__balance:,}")

    def withdraw(self, amount):
        if amount > self.__balance:
            print("❌ Insufficient funds!")
            return
        self.__balance -= amount
        print(f"✅ Withdrawn PKR {amount:,}. Remaining: PKR {self.__balance:,}")

    def check_password(self, attempt):
        return self.__password == attempt  # Can check, but never EXPOSE the password


acc = UserAccount("awais", "secret123", 10000)

# ✅ Public access works fine
print(acc.username)          # awais

# ✅ Use methods to interact with private data
acc.deposit(5000)            # ✅ Deposited PKR 5,000. New balance: PKR 15,000
acc.withdraw(20000)          # ❌ Insufficient funds!
print(acc.get_balance())     # 15000

# ❌ Direct access to private fails (name mangling)
# print(acc.__balance)       # AttributeError!
# print(acc.__password)      # AttributeError!
```

### 🌐 Real Website Example — PayPal / JazzCash

Any payment platform uses encapsulation heavily. Your actual balance and card
numbers are private attributes. You only interact with them through safe methods:

```python
class PaymentWallet:
    def __init__(self, user_id, card_number, balance):
        self.__user_id     = user_id
        self.__card_number = card_number  # NEVER exposed directly
        self.__balance     = balance
        self.__transactions = []

    def get_masked_card(self):
        # Show only last 4 digits — never the full card
        return f"**** **** **** {self.__card_number[-4:]}"

    def pay(self, merchant, amount):
        if amount > self.__balance:
            return "❌ Payment failed: Insufficient balance"
        self.__balance -= amount
        self.__transactions.append({"merchant": merchant, "amount": amount})
        return f"✅ Paid PKR {amount} to {merchant}"

    def get_balance(self):
        return self.__balance  # Controlled read

wallet = PaymentWallet("USR-007", "4111111111111234", 25000)

print(wallet.get_masked_card())        # **** **** **** 1234
print(wallet.pay("Daraz.pk", 3500))   # ✅ Paid PKR 3500 to Daraz.pk
print(wallet.get_balance())           # 21500
# wallet.__balance = 9999999          # ❌ This won't work — data is protected
```

---

## 🔵 Abstraction — "Show What, Hide How"

### Plain English Explanation

**Abstraction** means hiding the complex internal implementation and showing
only the essential, simplified interface to the user.

You define **abstract methods** (methods with no body) in a parent class to say
"every child class MUST implement this method." The abstract class acts as a
contract — it forces every subclass to follow the rules.

In Python, abstraction is implemented using the `abc` module (`ABC` =
Abstract Base Class).

### 🧒 Explain Like I'm a Child

When you press the power button on your TV, do you know what happens inside?
The circuit logic, the voltage, the signal processing? No — and you don't
need to! You just press the button and it works.

The TV remote is the abstraction — it hides all the complex stuff and gives
you simple buttons to press. The abstract class is like the remote's design:
it says "every TV must have a power button and a volume button" — but each TV
brand implements them differently inside.

### Key Rule

- You **cannot create an object** from an abstract class directly
- Any class that inherits from it **must implement** all abstract methods
- If it doesn't implement them, Python will raise a `TypeError`

### Detailed Example

```python
from abc import ABC, abstractmethod

class PaymentMethod(ABC):               # Abstract class — the CONTRACT
    """
    Every payment method MUST implement these methods.
    This class cannot be instantiated directly.
    """

    @abstractmethod
    def process_payment(self, amount):  # No body — child MUST implement
        pass

    @abstractmethod
    def refund(self, amount):           # No body — child MUST implement
        pass

    def get_type(self):                 # Regular method — shared, not abstract
        return self.__class__.__name__


# ✅ Concrete class — implements all abstract methods
class CreditCard(PaymentMethod):
    def __init__(self, card_number):
        self.card_number = card_number

    def process_payment(self, amount):      # ✅ Must implement this
        print(f"💳 Credit card charged PKR {amount:,}")
        return True

    def refund(self, amount):               # ✅ Must implement this
        print(f"💳 Credit card refunded PKR {amount:,}")


# ✅ Another concrete class — different implementation
class EasyPaisa(PaymentMethod):
    def __init__(self, phone):
        self.phone = phone

    def process_payment(self, amount):      # ✅ Different implementation
        print(f"📱 EasyPaisa transfer PKR {amount:,} from {self.phone}")
        return True

    def refund(self, amount):               # ✅ Different implementation
        print(f"📱 EasyPaisa refund PKR {amount:,} to {self.phone}")


# ❌ Cannot instantiate abstract class
# p = PaymentMethod()     # TypeError: Can't instantiate abstract class

# ✅ Use concrete classes
cc  = CreditCard("4111-1234")
ep  = EasyPaisa("+92300-1234567")

cc.process_payment(5000)    # 💳 Credit card charged PKR 5,000
ep.process_payment(1200)    # 📱 EasyPaisa transfer PKR 1,200...
cc.refund(500)
ep.refund(200)

print(cc.get_type())        # CreditCard  ← from the shared non-abstract method
print(ep.get_type())        # EasyPaisa
```

### 🌐 Real Website Example — Stripe / Payment Gateways

Stripe processes payments from cards, bank transfers, Apple Pay, etc. Each is a
different class but all follow the same abstract interface:

```python
from abc import ABC, abstractmethod

class PaymentGateway(ABC):          # Abstract interface
    @abstractmethod
    def charge(self, amount, currency):
        pass

    @abstractmethod
    def verify(self):
        pass

class StripeCard(PaymentGateway):
    def charge(self, amount, currency):
        print(f"Stripe: Charging {currency} {amount} via credit card")

    def verify(self):
        print("Stripe: Verifying card details via PCI-DSS")

class PayPalGateway(PaymentGateway):
    def charge(self, amount, currency):
        print(f"PayPal: Processing {currency} {amount} via PayPal account")

    def verify(self):
        print("PayPal: Verifying PayPal token")

# The checkout system doesn't care WHICH gateway — just that it has charge()
def checkout(gateway: PaymentGateway, amount):
    gateway.verify()
    gateway.charge(amount, "PKR")

checkout(StripeCard(), 10000)
checkout(PayPalGateway(), 10000)
```

---

## 🔵 Polymorphism — "Many Forms"

### Plain English Explanation

**Polymorphism** means the ability for different objects to respond to the
**same method call** in their **own way**. The method name is the same, but
the behavior differs depending on what object is calling it.

This lets you write code that works with many different types of objects without
knowing their exact class — as long as they all have the expected method.

### 🧒 Explain Like I'm a Child

Think about the word "speak." When your dog speaks, it barks. When your cat
speaks, it meows. When your parrot speaks, it talks. Same action ("speak"),
different behavior per animal. That's polymorphism!

### Two Types of Polymorphism

| Type | How | Example |
|---|---|---|
| **Method Overriding** | Child redefines a parent's method | `Dog.speak()` overrides `Animal.speak()` |
| **Duck Typing** | Different classes, same method name — no inheritance needed | `cat.speak()` and `dog.speak()` |

### Method Overriding Example

```python
class Notification:                         # Parent
    def __init__(self, recipient, message):
        self.recipient = recipient
        self.message   = message

    def send(self):                         # Base behavior
        print(f"Sending: {self.message}")


class EmailNotification(Notification):      # Child 1 — overrides send()
    def send(self):
        print(f"📧 EMAIL to {self.recipient}: {self.message}")
        print("    [Delivered via SMTP server]")


class SMSNotification(Notification):        # Child 2 — overrides send()
    def send(self):
        print(f"📱 SMS to {self.recipient}: {self.message}")
        print("    [Delivered via Twilio API]")


class PushNotification(Notification):       # Child 3 — overrides send()
    def send(self):
        print(f"🔔 PUSH to {self.recipient}: {self.message}")
        print("    [Delivered via Firebase]")


# Polymorphism in action — same method name, different behavior
notifications = [
    EmailNotification("awais@gmail.com", "Order shipped!"),
    SMSNotification("+923001234567",      "Order shipped!"),
    PushNotification("device-token-xyz",  "Order shipped!"),
]

for notif in notifications:
    notif.send()        # Each calls its own version of send()
    print()
```

### Duck Typing Example (no inheritance needed)

```python
# "If it walks like a duck and quacks like a duck, it's a duck"
# Python doesn't care about the TYPE — just whether the method EXISTS

class PDFReport:
    def generate(self):
        print("📄 Generating PDF report...")

class ExcelReport:
    def generate(self):
        print("📊 Generating Excel spreadsheet...")

class HTMLReport:
    def generate(self):
        print("🌐 Generating HTML dashboard...")

# No inheritance relationship at all — just same method name
def export_report(report):          # Works with ANY object that has generate()
    report.generate()

export_report(PDFReport())          # 📄 Generating PDF report...
export_report(ExcelReport())        # 📊 Generating Excel spreadsheet...
export_report(HTMLReport())         # 🌐 Generating HTML dashboard...
```

### 🌐 Real Website Example — Google Docs / Microsoft 365

Document export features use polymorphism — same `export()` call, different output:

```python
class Document:
    def __init__(self, title, content):
        self.title   = title
        self.content = content

    def export(self):
        raise NotImplementedError

class GoogleDoc(Document):
    def export(self):
        print(f"☁️ Uploading '{self.title}' to Google Drive as .gdoc")

class WordDoc(Document):
    def export(self):
        print(f"💼 Saving '{self.title}' as {self.title}.docx")

class MarkdownDoc(Document):
    def export(self):
        print(f"📝 Writing '{self.title}' as {self.title}.md")

docs = [
    GoogleDoc("OOP Notes", "...content..."),
    WordDoc("Resume", "...content..."),
    MarkdownDoc("README", "...content..."),
]

for doc in docs:
    doc.export()        # Same call — different behavior per class
```

---

---

# 3.5 Method Types

## Plain English Explanation

Inside a class, there are **three types of methods**. They differ in what they
have access to and how they are called:

| Method Type | Has access to | First Param | Decorator | Called via |
|---|---|---|---|---|
| **Instance method** | Instance (`self`) + Class | `self` | None | `object.method()` |
| **Class method** | Class only (`cls`) | `cls` | `@classmethod` | `Class.method()` |
| **Static method** | Neither | Nothing | `@staticmethod` | `Class.method()` |

---

## 🔵 Instance Method — "Belongs to the Object"

### Explanation

The most common type. It takes `self` as the first parameter, giving it access
to the specific object's data. It can read and modify instance attributes.

### 🧒 Like a Child

Your phone has a "Send Message" feature. When YOU press it, it sends YOUR
message from YOUR account. The action depends on YOUR specific phone data.
That's an instance method — it works on the specific object (your phone).

### Example

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner   = owner
        self.balance = balance

    def deposit(self, amount):          # Instance method
        self.balance += amount
        print(f"[{self.owner}] Deposited PKR {amount}. Balance: PKR {self.balance}")

    def withdraw(self, amount):         # Instance method
        if amount > self.balance:
            print(f"[{self.owner}] ❌ Insufficient balance!")
        else:
            self.balance -= amount
            print(f"[{self.owner}] Withdrew PKR {amount}. Balance: PKR {self.balance}")

acc1 = BankAccount("Awais", 10000)
acc2 = BankAccount("Ali",    5000)

acc1.deposit(3000)    # [Awais] Deposited PKR 3000. Balance: PKR 13000
acc2.withdraw(1000)   # [Ali] Withdrew PKR 1000. Balance: PKR 4000

# acc1 and acc2 are INDEPENDENT — depositing to acc1 doesn't affect acc2
```

---

## 🔵 Class Method — "Belongs to the Class"

### Explanation

A class method works on the **class itself**, not on any specific instance. It uses
`cls` (class) instead of `self`. It can read and modify class-level data.

Use it when:
- You want to create objects in alternative ways (factory methods)
- You want to access or modify class attributes

### 🧒 Like a Child

Imagine a school that tracks how many students have enrolled. No specific
student owns this count — the **school itself** owns it. A class method is
like asking the school "how many students do you have?" — not asking a
specific student.

### Example

```python
class Employee:
    company    = "TechCorp Lahore"      # Class attribute
    emp_count  = 0                       # Tracks how many employees created

    def __init__(self, name, department):
        self.name       = name
        self.department = department
        Employee.emp_count += 1          # Increment class-level counter

    def introduce(self):                  # Instance method
        print(f"Hi, I'm {self.name} from {self.department} at {Employee.company}")

    @classmethod
    def get_company(cls):                 # Class method — works on class data
        return cls.company

    @classmethod
    def change_company(cls, new_name):    # Class method — modifies class data
        cls.company = new_name
        print(f"Company renamed to: {cls.company}")

    @classmethod
    def from_string(cls, data_string):    # Factory method — alternative constructor!
        name, dept = data_string.split(",")
        return cls(name.strip(), dept.strip())  # Creates and returns a new object


e1 = Employee("Awais", "Backend Dev")
e2 = Employee("Sara",  "UI/UX Design")
e3 = Employee.from_string("Ali, Data Science")   # Alternative way to create

print(Employee.emp_count)       # 3
print(Employee.get_company())   # TechCorp Lahore

Employee.change_company("InnoTech Solutions")
e1.introduce()   # Hi, I'm Awais from Backend Dev at InnoTech Solutions
```

### 🌐 Real Website Example — Django's ORM / Database Models

Django's model system uses class methods to query the database. You ask the
class (not an instance) to find data:

```python
class Product(Model):
    name     = CharField(max_length=200)
    price    = DecimalField()
    in_stock = BooleanField(default=True)

    @classmethod
    def get_available(cls):
        return cls.objects.filter(in_stock=True)    # Queries the class's table

    @classmethod
    def create_discounted(cls, name, original_price, discount_pct):
        discounted = original_price * (1 - discount_pct / 100)
        return cls.objects.create(name=name, price=discounted)

# Usage — called on the CLASS, not on any specific product
available = Product.get_available()
sale_item = Product.create_discounted("Python Book", 2000, 20)
```

---

## 🔵 Static Method — "Just a Helper, Lives in the Class"

### Explanation

A static method doesn't take `self` or `cls`. It has **no access** to instance
or class data. It's basically a regular function that logically belongs to the
class (grouped with it for organization) but doesn't need any class/object data.

Use it for **utility functions** — calculations, formatting, validation — that
are related to the class but don't need its data.

### 🧒 Like a Child

A calculator inside a school. It belongs to the school (lives there), but
when you use it to calculate 2+2, it doesn't need to know the school's name
or how many students are enrolled. It's just a tool that does math.

### Example

```python
class TemperatureConverter:
    def __init__(self, value, unit):
        self.value = value
        self.unit  = unit

    def describe(self):                     # Instance method
        print(f"Temperature: {self.value}°{self.unit}")

    @staticmethod
    def celsius_to_fahrenheit(c):           # Static — no self or cls needed
        return (c * 9/5) + 32

    @staticmethod
    def fahrenheit_to_celsius(f):           # Static — pure calculation
        return (f - 32) * 5/9

    @staticmethod
    def is_freezing(celsius):               # Static — pure logic
        return celsius <= 0


# Can call static methods WITHOUT creating an object
print(TemperatureConverter.celsius_to_fahrenheit(100))   # 212.0
print(TemperatureConverter.fahrenheit_to_celsius(98.6))  # 37.0
print(TemperatureConverter.is_freezing(-5))              # True

# OR call them on an instance (both work)
t = TemperatureConverter(37, "C")
print(t.celsius_to_fahrenheit(37))                       # 98.6
```

### 🌐 Real Website Example — Uber / Careem (Ride-hailing)

Static methods are used for utility calculations — like fare estimation:

```python
class RideCalculator:
    BASE_FARE    = 50      # PKR — class attribute
    RATE_PER_KM  = 25      # PKR/km

    def __init__(self, ride_id, distance, driver):
        self.ride_id  = ride_id
        self.distance = distance
        self.driver   = driver

    def calculate_fare(self):               # Instance method — uses self.distance
        return self.BASE_FARE + (self.distance * self.RATE_PER_KM)

    @staticmethod
    def estimate_time(distance_km, speed_kmh=30):  # Static — pure math
        minutes = (distance_km / speed_kmh) * 60
        return round(minutes)

    @staticmethod
    def format_currency(amount):            # Static — pure formatting
        return f"PKR {amount:,.0f}"

    @staticmethod
    def validate_distance(km):             # Static — pure validation
        return 0 < km <= 500


ride = RideCalculator("RIDE-001", 12, "Ahmed")
fare = ride.calculate_fare()

print(RideCalculator.format_currency(fare))          # PKR 350
print(RideCalculator.estimate_time(12))              # 24  (minutes)
print(RideCalculator.validate_distance(600))         # False
```

---

---

# 3.6 Special (Dunder) Methods

## Plain English Explanation

**Dunder methods** ("double underscore" methods, also called "magic methods")
are special methods that Python calls **automatically** in response to built-in
operations or functions.

You define them in your class to control how your objects behave when Python's
built-in operations are used on them — like `print()`, `len()`, `+`, `==`, etc.

They always start and end with double underscores: `__method__`

## 🧒 Explain Like I'm a Child

Imagine you're playing a video game. When you press the "A" button, the character
jumps. When you press "B," they attack. The buttons are fixed (built-in
operations). But the game developer programmed what happens when each button is
pressed. Dunder methods are your way of programming what happens when Python
presses its "buttons" on your objects.

---

## 🔵 `__str__()` — "How to Print This Object"

### Explanation

Python calls `__str__()` when you use `print(obj)` or `str(obj)`.
It should return a **human-friendly string** — something a user would want to see.

### Without `__str__`

```python
class Product:
    def __init__(self, name, price):
        self.name  = name
        self.price = price

p = Product("Python Book", 2000)
print(p)    # <__main__.Product object at 0x7f4e2a3b1c90>  ← Useless!
```

### With `__str__`

```python
class Product:
    def __init__(self, name, price):
        self.name  = name
        self.price = price

    def __str__(self):              # Called by print() and str()
        return f"📦 {self.name} — PKR {self.price:,}"

p = Product("Python Book", 2000)
print(p)        # 📦 Python Book — PKR 2,000  ← Now useful!
print(str(p))   # 📦 Python Book — PKR 2,000  ← Same thing
```

### 🌐 Real Website Example — Django Admin Panel

Django's admin uses `__str__` to display objects in the admin interface.
Without it, you'd see `<Product object (1)>`. With it, you see "Python Book":

```python
class Product(models.Model):
    name  = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return f"{self.name} (PKR {self.price})"
    # Admin panel shows: "Python Book (PKR 2000)"
    # instead of: "Product object (1)"
```

---

## 🔵 `__repr__()` — "How to Represent This for Developers"

### Explanation

Python calls `__repr__()` in the interactive console (REPL), in debugging tools,
and when `repr(obj)` is called. It should return a **precise, unambiguous string**
that ideally shows how to recreate the object.

**Rule of thumb:**
- `__str__` → for **users** (readable, friendly)
- `__repr__` → for **developers** (precise, debuggable)

If `__str__` is not defined, Python falls back to `__repr__`.

### Example

```python
import datetime

class LogEntry:
    def __init__(self, level, message):
        self.level     = level
        self.message   = message
        self.timestamp = datetime.datetime.now()

    def __str__(self):
        # What a user sees in a log viewer
        return f"[{self.level}] {self.message}"

    def __repr__(self):
        # What a developer sees in debug mode
        return (
            f"LogEntry("
            f"level='{self.level}', "
            f"message='{self.message}', "
            f"timestamp='{self.timestamp}')"
        )

log = LogEntry("ERROR", "Database connection failed")

print(log)          # [ERROR] Database connection failed     ← __str__ (user)
print(repr(log))    # LogEntry(level='ERROR', ...)           ← __repr__ (dev)

# In a list, Python uses __repr__
logs = [log]
print(logs)         # [LogEntry(level='ERROR', ...)]         ← __repr__
```

### 🌐 Real Website Example — Debugging a Flask/Django API

When a server-side error occurs and you inspect objects in a debugger or log file,
`__repr__` gives you the full picture:

```python
class APIRequest:
    def __init__(self, method, endpoint, user_id, payload):
        self.method   = method
        self.endpoint = endpoint
        self.user_id  = user_id
        self.payload  = payload

    def __str__(self):
        return f"{self.method} {self.endpoint}"  # Brief for logs

    def __repr__(self):
        # Full details for debugging
        return (
            f"APIRequest(method='{self.method}', "
            f"endpoint='{self.endpoint}', "
            f"user_id={self.user_id}, "
            f"payload={self.payload})"
        )

req = APIRequest("POST", "/api/orders", 42, {"item": "laptop", "qty": 1})

# In log files — brief
print(f"Incoming request: {req}")           # POST /api/orders

# In debug console — full info
print(repr(req))
# APIRequest(method='POST', endpoint='/api/orders', user_id=42, payload={...})
```

---

## 🔵 `__len__()` — "What is the Length of This Object?"

### Explanation

Python calls `__len__()` when you use `len(obj)`. You define it to say what
"length" means for your custom object. Without it, calling `len()` on your
object raises a `TypeError`.

### 🧒 Like a Child

For a regular list, `len()` tells you how many items are in it. For YOUR custom
class, you decide what counts as "length" — it could be number of items in a
cart, number of posts in a blog, number of characters in a text, etc.

### Example

```python
class ShoppingCart:
    def __init__(self, user):
        self.user  = user
        self.items = []

    def add_item(self, item_name, price, qty=1):
        self.items.append({
            "name":  item_name,
            "price": price,
            "qty":   qty
        })

    def __len__(self):
        # "Length" of a cart = total number of item types in it
        return len(self.items)

    def __str__(self):
        total = sum(i["price"] * i["qty"] for i in self.items)
        return f"🛒 {self.user}'s cart: {len(self)} items | Total: PKR {total:,}"


cart = ShoppingCart("Awais")
print(len(cart))    # 0  — empty cart

cart.add_item("Python Book",  2000, 1)
cart.add_item("USB Keyboard", 3500, 2)
cart.add_item("Laptop Stand", 1800, 1)

print(len(cart))    # 3  — three different items
print(cart)         # 🛒 Awais's cart: 3 items | Total: PKR 10,800

# Now you can use cart in boolean checks (empty = False, non-empty = True)
if cart:
    print("Cart has items — proceed to checkout!")
```

### 🌐 Real Website Example — Daraz / Amazon Shopping Cart

Every e-commerce site uses `__len__` (or equivalent) for the cart badge (the
small number you see on the cart icon showing how many items you have):

```python
class Cart:
    def __init__(self, session_id):
        self.session_id = session_id
        self._items     = {}            # {product_id: {"name": ..., "qty": ...}}

    def add(self, product_id, name, price, qty=1):
        if product_id in self._items:
            self._items[product_id]["qty"] += qty
        else:
            self._items[product_id] = {"name": name, "price": price, "qty": qty}

    def __len__(self):
        # Total quantity across all items (what the badge shows)
        return sum(item["qty"] for item in self._items.values())

    def __str__(self):
        return f"Cart ({len(self)} items)"


cart = Cart("sess-abc123")
cart.add("P001", "Laptop",   85000, 1)
cart.add("P002", "Mouse",    1500,  2)
cart.add("P001", "Laptop",   85000, 1)   # Adding same item again → qty becomes 2

print(len(cart))    # 4  — shown on cart icon badge (2 laptops + 2 mice)
print(str(cart))    # Cart (4 items)
```

---

### 📊 Summary Table — Dunder Methods

| Method | Built-in Trigger | Returns | Use For |
|---|---|---|---|
| `__str__()` | `print(obj)`, `str(obj)` | Friendly string | User-facing display |
| `__repr__()` | `repr(obj)`, REPL, lists | Debug string | Developer debugging |
| `__len__()` | `len(obj)` | Integer | Item count, size |
| `__eq__(other)` | `obj1 == obj2` | Boolean | Equality comparison |
| `__lt__(other)` | `obj1 < obj2` | Boolean | Sorting, comparison |
| `__add__(other)` | `obj1 + obj2` | New object | Adding objects |

---

---

# 3.7 `super()` and MRO

## 🔵 `super()` — "Let the Parent Do Its Part First"

### Plain English Explanation

`super()` is a built-in function that gives you access to the **parent class**
from within a child class. The most common use is calling the parent's
`__init__()` from the child's `__init__()` — so the parent sets up its part,
and then the child adds its own part on top.

Without `super()`, you'd have to manually call the parent class by name, which
breaks if you refactor or use multiple inheritance.

### 🧒 Explain Like I'm a Child

Imagine a job application form. Your dad already filled in the family details
section. When it's your turn, you say "Hey dad, please fill in your part first."
Then you add YOUR own personal details on top.

`super().__init__()` = "Hey parent class, please run YOUR setup first."

### Without super() — The Problem

```python
class Animal:
    def __init__(self, name, species):
        self.name    = name
        self.species = species
        print(f"Animal setup: {name} ({species})")

class Dog(Animal):
    def __init__(self, name, breed):
        # ❌ Manually calling parent — fragile, breaks with multiple inheritance
        Animal.__init__(self, name, "Canis familiaris")
        self.breed = breed
```

### With super() — The Correct Way

```python
class Animal:
    def __init__(self, name, species):
        self.name    = name
        self.species = species
        print(f"Animal setup: {name} ({species})")

    def breathe(self):
        print(f"{self.name} is breathing")


class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name, "Canis familiaris")  # ✅ Parent runs first
        self.breed = breed                           # Then child adds its part
        print(f"Dog setup: breed = {breed}")

    def bark(self):
        print(f"{self.name} ({self.breed}) says: Woof!")


class ServiceDog(Dog):
    def __init__(self, name, breed, task):
        super().__init__(name, breed)               # ✅ Calls Dog.__init__
        self.task = task                             # Grandchild adds its part
        print(f"ServiceDog setup: task = {task}")

    def perform_duty(self):
        print(f"{self.name} is performing: {self.task}")


# Watch the order of constructor calls:
sd = ServiceDog("Max", "German Shepherd", "Guide for visually impaired")
# Animal setup: Max (Canis familiaris)     ← Grandparent first
# Dog setup: breed = German Shepherd       ← Parent second
# ServiceDog setup: task = Guide...        ← Child last

sd.breathe()        # Inherited from Animal
sd.bark()           # Inherited from Dog
sd.perform_duty()   # Own method
```

### Overriding Methods with super()

`super()` is also used when **overriding methods** — you want the parent's
behavior PLUS something extra:

```python
class DatabaseLogger:
    def log(self, message):
        print(f"[DB] {message}")

class TimestampedLogger(DatabaseLogger):
    def log(self, message):
        import datetime
        timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        super().log(f"[{timestamp}] {message}")   # Parent logs, then we add timestamp

class UserActivityLogger(TimestampedLogger):
    def log(self, user_id, action):
        super().log(f"User {user_id}: {action}")  # Adds user context


logger = UserActivityLogger()
logger.log("USR-42", "Placed an order")
# [DB] [2025-06-04 14:30:00] User USR-42: Placed an order
```

### 🌐 Real Website Example — Django Model Inheritance

Django uses `super().__init__()` constantly when extending models and views:

```python
from django.db import models

class BaseModel(models.Model):
    """Every model in the app gets these fields automatically"""
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    is_deleted = models.BooleanField(default=False)

    class Meta:
        abstract = True

    def soft_delete(self):
        self.is_deleted = True
        self.save()

class Product(BaseModel):          # Inherits created_at, updated_at, is_deleted
    name  = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)

    def save(self, *args, **kwargs):
        # Run custom logic BEFORE saving
        self.name = self.name.strip().title()  # Clean the name
        super().save(*args, **kwargs)          # ✅ Then call parent's save()
```

---

## 🔵 Method Resolution Order (MRO)

### Plain English Explanation

**MRO (Method Resolution Order)** is the order in which Python searches through
classes to find a method when it's called on an object.

When you call `obj.some_method()`, Python needs to find which class has that
method. If the object's class doesn't have it, Python looks in the parent
classes — but in what order? That order is the MRO.

Python uses the **C3 Linearization algorithm** to compute the MRO. The rule is:
- Check the current class first
- Then go left to right through parents
- Never check a class before all its subclasses have been checked

### 🧒 Explain Like I'm a Child

You're lost and need directions. You ask:
1. First, look it up yourself (your own class)
2. If you don't know, ask your dad (first parent — left side)
3. If dad doesn't know, ask your uncle (second parent — right side)
4. If nobody knows, ask your grandparent (common ancestor)

MRO is that exact searching order — written out explicitly.

### The Diamond Problem — Why MRO Matters

```
        A
       / \
      B   C
       \ /
        D
```

Without MRO rules, calling `D.hello()` is ambiguous — should it use B's version
or C's version? MRO resolves this by establishing a strict, consistent order.

### Detailed MRO Example

```python
class A:
    def greet(self):
        print("Hello from A")

    def info(self):
        print("Info from A")

class B(A):
    def greet(self):
        print("Hello from B")

class C(A):
    def greet(self):
        print("Hello from C")

    def info(self):
        print("Info from C")

class D(B, C):                  # D inherits from B first, then C
    pass


d = D()
d.greet()   # Hello from B     ← D → B is checked before D → C
d.info()    # Info from C      ← D has no info(), B has no info(), found in C


# Let's see the exact MRO chain:
print(D.__mro__)
# (<class 'D'>, <class 'B'>, <class 'C'>, <class 'A'>, <class 'object'>)

# Readable format:
for i, cls in enumerate(D.__mro__):
    print(f"  Step {i+1}: Look in {cls.__name__}")
# Step 1: Look in D
# Step 2: Look in B
# Step 3: Look in C
# Step 4: Look in A
# Step 5: Look in object  ← Python's ultimate base class
```

### MRO with super() in Multiple Inheritance

`super()` is MRO-aware — it doesn't just call the immediate parent, it calls
the NEXT class in the MRO chain. This ensures each class in the chain is called
exactly once:

```python
class A:
    def process(self):
        print("A processing")

class B(A):
    def process(self):
        print("B processing")
        super().process()       # Calls C (next in MRO), not A directly!

class C(A):
    def process(self):
        print("C processing")
        super().process()       # Calls A (next in MRO)

class D(B, C):
    def process(self):
        print("D processing")
        super().process()       # Calls B (next in MRO)

d = D()
d.process()
# D processing   ← D.process() called
# B processing   ← super() in D calls B (next in MRO)
# C processing   ← super() in B calls C (next in MRO — not A!)
# A processing   ← super() in C calls A

# MRO: D → B → C → A → object
print([cls.__name__ for cls in D.__mro__])
# ['D', 'B', 'C', 'A', 'object']
```

### 🌐 Real Website Example — Django Mixin-Based Views

Django's view system uses MRO-aware `super()` calls extensively. Multiple
mixins chain together cleanly because of MRO:

```python
class LoginRequiredMixin:
    def dispatch(self, request, *args, **kwargs):
        if not request.user.is_authenticated:
            return redirect("/login/")
        return super().dispatch(request, *args, **kwargs)  # Next in MRO

class PermissionRequiredMixin:
    required_permission = None
    def dispatch(self, request, *args, **kwargs):
        if not request.user.has_perm(self.required_permission):
            return redirect("/forbidden/")
        return super().dispatch(request, *args, **kwargs)  # Next in MRO

class View:
    def dispatch(self, request, *args, **kwargs):
        return self.get(request, *args, **kwargs)


class AdminDashboard(LoginRequiredMixin, PermissionRequiredMixin, View):
    required_permission = "admin.view_dashboard"

    def get(self, request):
        return render_dashboard()

# MRO: AdminDashboard → LoginRequired → PermissionRequired → View → object
# When dispatch() is called:
# 1. LoginRequiredMixin checks if logged in
# 2. PermissionRequiredMixin checks permissions
# 3. View actually handles the request
# — all chained via super() following MRO order
```

### 📊 MRO Lookup Steps Table

| Step | Class Checked | Reason |
|---|---|---|
| 1 | `D` (the object's class) | Always start with the current class |
| 2 | `B` (first parent, left side) | Left-to-right order among parents |
| 3 | `C` (second parent, right side) | Left-to-right order continues |
| 4 | `A` (common ancestor) | After all subclasses are checked |
| 5 | `object` | Python's ultimate base class — everyone inherits this |

---

---

# 📊 Master Reference Tables

## All OOP Concepts at a Glance

| Concept | Purpose | Python Syntax | Real Example |
|---|---|---|---|
| **Class** | Define a blueprint | `class User:` | User accounts on Facebook |
| **Object** | Create from blueprint | `u = User()` | A specific user profile |
| **Instance attr** | Data per object | `self.name = name` | Your unique profile name |
| **Class attr** | Data for all objects | `platform = "Facebook"` | Platform name shared by all |
| **Method** | Object's action | `def post(self):` | Posting a status |
| **Constructor** | Auto setup | `def __init__(self):` | Auto-fill account creation date |
| **Single inherit** | One parent | `class Admin(User):` | Admin extends User |
| **Multiple inherit** | Two+ parents | `class Duck(Fly, Swim):` | Notification system |
| **Multilevel** | Chain of inheritance | `A → B → C` | Django's CBV chain |
| **Encapsulation** | Hide & protect data | `self.__password` | Hidden bank balance |
| **Abstraction** | Define rules for child | `@abstractmethod` | Payment gateway interface |
| **Polymorphism** | Same name, diff behavior | Method overriding | Notification channels |
| **Instance method** | Works on object | `def f(self):` | `tweet.like()` |
| **Class method** | Works on class | `@classmethod def f(cls):` | `Product.get_all()` |
| **Static method** | Utility, no context | `@staticmethod def f():` | `RideCalc.estimate_time()` |
| **`__str__`** | User-friendly print | `def __str__(self):` | Django admin labels |
| **`__repr__`** | Dev-friendly debug | `def __repr__(self):` | Log entry details |
| **`__len__`** | Length of object | `def __len__(self):` | Cart item count badge |
| **`super()`** | Call parent method | `super().__init__()` | Django model `save()` |
| **MRO** | Method search order | `Class.__mro__` | Django mixin dispatch chain |

---

## ⚡ Complete Cheat Sheet

```python
# ── CLASS & OBJECT ─────────────────────────────────────────────────
class Product:
    category = "General"                 # Class attribute (shared)

    def __init__(self, name, price):     # Constructor — auto runs
        self.name  = name                # Instance attribute (unique)
        self.price = price

p = Product("Laptop", 85000)            # Create object → __init__ fires

# ── INHERITANCE ─────────────────────────────────────────────────────
class Electronics(Product):             # Single inheritance
    def __init__(self, name, price, warranty):
        super().__init__(name, price)    # Call parent's __init__
        self.warranty = warranty

# ── OOP PILLARS ──────────────────────────────────────────────────────
# Encapsulation
self.__pin = "1234"                     # Private — hidden outside class
self._balance = 1000                    # Protected — convention only

# Abstraction
from abc import ABC, abstractmethod
class Shape(ABC):
    @abstractmethod
    def area(self): pass                # Child MUST implement

# Polymorphism
class Dog:
    def speak(self): print("Woof")
class Cat:
    def speak(self): print("Meow")
for a in [Dog(), Cat()]:
    a.speak()                           # Same call, different output

# ── METHOD TYPES ─────────────────────────────────────────────────────
class Example:
    count = 0

    def instance_method(self):         # Access self (object data)
        return self.name

    @classmethod
    def class_method(cls):             # Access cls (class data)
        return cls.count

    @staticmethod
    def static_method(x, y):          # No self or cls — pure logic
        return x + y

# ── DUNDER METHODS ───────────────────────────────────────────────────
    def __str__(self):                 # print(obj) → user-friendly
        return f"Product: {self.name}"

    def __repr__(self):                # repr(obj) → dev-friendly
        return f"Product(name='{self.name}', price={self.price})"

    def __len__(self):                 # len(obj) → return integer
        return len(self.name)

# ── super() & MRO ────────────────────────────────────────────────────
super().__init__(args)                 # Call parent's constructor
super().method()                       # Call parent's method (MRO-aware)
ClassName.__mro__                      # View method resolution order
[c.__name__ for c in Class.__mro__]   # Readable MRO list
```