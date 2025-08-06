# Create a simple API with Django Rest Framework
We are going to create a simple API using `Django Rest Framework (DRF)`. This guide will walk you through the steps to set up DRF, create models, serializers, and views.

## Project setup

```bash
mkdir my_project
cd my_project

python3 -m venv env
source env/bin/activate

pip install django djangorestframework
django-admin startproject my_project . # remember to add the dot at the end

python manage.py startapp myapp
```

After running the above commands, your project structure should look like this:

```plaintext

my_project/
  |--------settings.py
  |--------__init__.py
  |--------urls.py
  |--------asgi.py
  |--------wsgi.py
__pycache__/
  |------------__init__.cpython-312.pyc
  |------------settings.cpython-312.pyc
myapp/
  |--------apps.py
  |--------__init__.py
  |--------admin.py
  |--------models.py
  |--------tests.py
  |--------views.py
migrations/
  |------------__init__.py
env/...
manage.py
```

### `settings.py` necessary changes:

```python
# my_project/settings.py
# Add 'myapp' and 'rest_framework' to INSTALLED_APPS

INSTALLED_APPS = [
    ...
    'rest_framework',
    'myapp',
]

```

## `models.py` setup models for this guide
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
## Migrations
After defining your models, you need to create and apply migrations:
```bash
python manage.py makemigrations
python manage.py migrate
``` 
     
Now that our models are set up, we can create serializers and views to expose these models through an API.

## Serializers
Serializers in DRF are used to convert complex data types, like Django models, into JSON or other content types. They also handle validation and deserialization.

Create a new file `serializers.py` in your `myapp` directory:

```python
# myapp/serializers.py
from rest_framework import serializers
from .models import Product, Order, OrderItem
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = '__all__'

class OrderItemSerializer(serializers.ModelSerializer):
    class Meta:
        model = OrderItem
        fields = '__all__'

class OrderSerializer(serializers.ModelSerializer):
    items = OrderItemSerializer(many=True, read_only=True)
    class Meta:
        model = Order
        fields = ['customer_name', 'items']
  
```

See the [official documentation](https://www.django-rest-framework.org/api-guide/serializers/) for more details on serializers.

## Views
Now, let's create views to handle requests for our models. Create a new file `views.py` in your `myapp` directory:
```python
# myapp/views.py
from rest_framework.generics import ListCreateAPIView, RetrieveUpdateDestroyAPIView
from .models import Product, Order, OrderItem
from .serializers import ProductSerializer, OrderSerializer, OrderItemSerializer

class ProductListCreateView(ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

class ProductDetailView(RetrieveUpdateDestroyAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

class OrderListCreateView(ListCreateAPIView):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer

class OrderDetailView(RetrieveUpdateDestroyAPIView):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer
```

We are using generic views from DRF to handle common operations like listing, creating, retrieving, updating, and deleting objects.
Here are official documentation links for [generic views](https://www.django-rest-framework.org/api-guide/generic-views/) that you can refer to.

## URLs
Next, we need to set up URLs for our API. Create a new file `urls.py` in your `myapp` directory:
```python
# myapp/urls.py
from django.urls import path
from .views import ProductListCreateView, ProductDetailView, OrderListCreateView, OrderDetailView
urlpatterns = [
    path('products/', ProductListCreateView.as_view(), name='product-list-create'),
    path('products/<int:pk>/', ProductDetailView.as_view(), name='product-detail'),
    path('orders/', OrderListCreateView.as_view(), name='order-list-create'),
    path('orders/<int:pk>/', OrderDetailView.as_view(), name='order-detail'),
]
```

Finally, include these URLs in your main `urls.py` file:
```python
# my_project/urls.py
from django.contrib import admin
from django.urls import path, include
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('myapp.urls')),  # Include the app's URLs
]
``` 

Now, you can run your Django server:
```bash
python manage.py runserver
```

Our current API endpoints will be:
- `/api/products/` - List and create products
- `/api/products/<int:pk>/` - Retrieve, update, or delete a specific product
- `/api/orders/` - List and create orders
- `/api/orders/<int:pk>/` - Retrieve, update, or delete a specific order

Optional, create a management command to populate the database with some initial data. Create a new file `management/commands/populate_db.py` in your `myapp` directory:

```python
# myapp/management/commands/populate_db.py
from django.core.management.base import BaseCommand
from myapp.models import Product, Order, OrderItem
class Command(BaseCommand):
    help = 'Populate the database with initial data'

    def handle(self, *args, **kwargs):
        # Create some products
        Product.objects.bulk_create([
            Product(name='Product 1', price=10.00, quantity=100),
            Product(name='Product 2', price=20.00, quantity=50),
            Product(name='Product 3', price=30.00, quantity=75),
        ])
        # Create an order
        order = Order.objects.create(customer_name='John Doe')
        # Create order items
        OrderItem.objects.bulk_create([
            OrderItem(order=order, product=Product.objects.get(name='Product 1'), quantity=2),
            OrderItem(order=order, product=Product.objects.get(name='Product 2'), quantity=1),
        ])
        self.stdout.write(self.style.SUCCESS('Successfully populated the database with initial data'))
```

Run the command to populate the database:
```bash
python manage.py populate_db
```

Now you have a simple API set up with Django Rest Framework, including models, serializers, views, and URLs. You can test your API using tools like Postman or curl.