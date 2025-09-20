# stripe payment gateway integration with Django

This guide is help implement stripe payment gateway integration step by step.
[Official Docs](https://docs.stripe.com/get-started)

## Index

1. [Setup stripe account](#setup-stripe-account)
2. [Setup project](#setup-project)
3. [Send API request](#send-api-request)
4. [WebHook](#webhook)

### Setup stripe account

I will not go into details about it right now. But basically we need to create a stripe account and get the test secret key from developer tab.

### Setup project

There is nothing much of setup for this. But for the sake of secret key safety we need to keep it in `.env` file and add `.env` to `.gitignore` or add the secret key in environment variables.

In the `settings.py` file,
```py

import os

# If we are using .env to store the secret key we need to load it with python-dotenv
# pip install python-dotenv - to install the package.
# from dotenv import load_dotenv
# load_dotenv() it will load the variables from .env file.

# Make sure the secret key is assigned to STRIPE_SECRATE_KEY in the environment variables. Else replace with the same name.
STRIPE_SECRATE_KEY = os.getenv("STRIPE_SECRATE_KEY")

```

### Send API request

We need to makesure we have `success_url`, `cancel_url` and `STRIPE_SECRATE_KEY`. 

In the `views.py`,

```py
import stripe
stripe.api_key = STRIPE_SECRATE_KEY

``` 

We need to create a session object and redirect to `session.url`

```py
session = stripe.checkout.Session.create(
    payment_method_types=['card'],
    line_items=[{
        # Add multiple items
        'price_data': {
            'currency': 'usd',
            'product_data':{
                'name': 'test product'
            },
            'unit_amount': 2000
        },
        'quantity': 1
    }],
    mode='payment',
    success_url='http://127.0.0.1:8000/success',
    cancel_url='http://127.0.0.1:8000/cencel'
)
return redirect(session.url)

```
This way, we can get payment from user and if the payment is successful it will be redirect automatically to the success url, then we can do necessary tasks like make the payement object in the database as Paid.

For testing we can use dummy card data to create payment. But it will only work with test mode.
[Official Docs](https://docs.stripe.com/testing) for testing data.

Example:
Visa card: 4242424242424242, CVC:Any 3 digits, Date: Any future date. 

### WebHook

[Official Docs](https://docs.stripe.com/webhooks/quickstart) for stripe webhook

#### How It Works

1. User starts payment → Your PaymentCreateView creates a Stripe Checkout Session.
2. Stripe processes payment → Once successful, Stripe sends an event to your webhook endpoint (e.g., /webhooks/stripe/).
3. Your webhook receives the event → You verify it, check if the payment was successful, and create the Enrollment object.

> For this example we will use Course and enrollment features to demostrate webhook.

First we have to make sure to create the stripe session properly and send course and user data with the session as metadata

```py
session = stripe.checkout.Session.create(
    payment_method_types=['card'],
    line_items=[{
        'price_data': {
            "currency": 'usd',
            "product_data": {
                "name": course_name,
            },
            "unit_amount": int(price*100),
        },
        'quantity': 1,
    }],
    mode='payment',
    success_url=request.build_absolute_uri(reverse('core:payment-success')),
    cancel_url=request.build_absolute_uri(reverse('core:payment-cancel')),
    metadata={
        "user_id": request.user.id,
        "course_id": course.id
    }
)

```

We need to create a webhook view which will be called by stripe after any event occures.

```py
# myapp/utils.py
import stripe
from django.views import View
from django.http import HttpResponse
from django.conf import settings
from django.views.decorators.csrf import csrf_exempt
from django.utils.decorators import method_decorator

from .models import Enrollment, Course
from django.contrib.auth import get_user_model

User = get_user_model()

stripe.api_key = settings.STRIPE_SECRET_KEY
endpoint_secret = settings.STRIPE_WEBHOOK_SECRET  # Get from Stripe Dashboard

@method_decorator(csrf_exempt, name='dispatch')
class StripeWebhookView(View):
    def post(self, request):
        payload = request.body
        sig_header = request.META.get('HTTP_STRIPE_SIGNATURE')
        event = None

        try:
            event = stripe.Webhook.construct_event(
                payload, sig_header, endpoint_secret
            )
        except ValueError:
            return HttpResponse(status=400)  # Invalid payload
        except stripe.error.SignatureVerificationError:
            return HttpResponse(status=400)  # Invalid signature

        # Handle the event type
        if event['type'] == 'checkout.session.completed':
            session = event['data']['object']
            user_id = session.get("metadata", {}).get("user_id")
            course_id = session.get("metadata", {}).get("course_id")

            if user_id and course_id:
                user = User.objects.get(id=user_id)
                course = Course.objects.get(id=course_id)

                # Avoid duplicate enrollment
                if not Enrollment.objects.filter(student=user, course=course).exists():
                    Enrollment.objects.create(student=user, course=course)

        return HttpResponse(status=200)

```
when stripe will get a successful payment it will trigger the webhook with the data. And this view will extract informations and create Enrollment object.

We need to add a url for this view. In the `project/urls.py`,
```py
# urls.py
from django.urls import path
from myapp.utils import StripeWebhookView

urlpatterns = [
    path('webhooks/stripe/', StripeWebhookView.as_view(), name='stripe-webhook'),
]

```

This will create url for `base_url/webhooks/stripe/`

#### Configure webhook in Stripe Dashboard

- Go to [Stripe Dashboard → Webhooks](https://dashboard.stripe.com/test/webhooks)
- Add endpoint: https://yourdomain.com/webhooks/stripe/
- Select event type: checkout.session.completed.
- Copy the signing secret and add it to settings.py as STRIPE_WEBHOOK_SECRET.

If our application is not hosted online instead running in localhost then stripe won't be able to find our webhook. Then we need install the stripe CLI in our local machine and start the listener.

Download and install stripe CLI:
[Stripe CLI](https://docs.stripe.com/stripe-cli/install)
follw the steps for specific operating system. After installing run the following command to verify installation.

```bash
stripe --version
```

If it's installed sucessfully then run the bellow command and follow the steps to login to your stripe account with the CLI.

```bash
stripe login
```
After login, start stripe listener:
```bash
stripe listen --forward-to localhost:8000/webhooks/stripe/
```

