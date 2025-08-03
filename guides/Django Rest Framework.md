# Django Rest Framework Guide

This guide is to help understand the common functionality of the Django Rest Framework (DRF).

it's created from the [official documentation](https://www.django-rest-framework.org/) and followed a youtube playlist [Django REST Framework tutorial by BugBytes](https://www.youtube.com/playlist?list=PL-2EBeDYMIbTLulc9FSoAXhbmXpLq2l5t).

## Index
1. [Installation](#installation)
2. [Setup DRF](#setup-drf)
3. [Creating a Simple API](#creating-a-simple-api)
4. [Model for this guide](#model-for-this-guide)
5. [Serializers](#serializers)
6. [django-silk](#django-silk)
7. [generic views](#generic-views)
8. [Permissions](#permissions)



### Installation
To install Django Rest Framework, you can use pip:

```bash
pip install djangorestframework
```

### Setup DRF

To use Django Rest Framework (DRF) we need a django project. If you don't have one, you can create it using:

```bash
django-admin startproject myproject
```
Then, navigate to your project directory:

```bash
cd myproject
```
Next, you need to add `rest_framework` to your `INSTALLED_APPS` in the `settings.py` file:

```python
# myproject/settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

After that, you can run the migrations to set up the database:

```bash
python manage.py migrate
```

Or, [Offcial documentation](https://www.django-rest-framework.org/tutorial/quickstart/) has a quick start guide that you can follow to set up DRF.

### Creating a Simple API

Follow the [Creating a Simple API](/guides/Django%20Rest%20Framework/Create%20a%20Simple%20API.md) guide to create a simple API with Django Rest Framework.

Or, follow [DRF Quickstart](https://www.django-rest-framework.org/tutorial/quickstart/) from the official documentation to create a simple API.

### Model for this guide
For this guide, we will use some simple models. `Product`, `Order` and `OrderItem`. Here is the code for the models:

```python
# myapp/models.py
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    quantity = models.PositiveIntegerField()

    def __str__(self):
        return self.name

class Order(models.Model):
    customer_name = models.CharField(max_length=100)
    created_at = models.DateTimeField(auto_now_add=True)
    def __str__(self):
        return f'Order of {self.customer_name}'

class OrderItem(models.Model):
    order = models.ForeignKey(Order, related_name='items', on_delete=models.CASCADE)
    product = models.ForeignKey(Product, related_name='order_items', on_delete=models.CASCADE)
    quantity = models.PositiveIntegerField()
    def __str__(self):
        return f'{self.quantity} of {self.product.name} in {self.order}'
```
