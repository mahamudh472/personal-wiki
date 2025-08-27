# stripe payment gateway integration with Django

This guide is help implement stripe payment gateway integration step by step.
[Official Docs](https://docs.stripe.com/get-started)

## Index

1. [Setup stripe account](#setup-stripe-account)
2. [Setup project](#setup-project)
3. [Send API request](#send-api-request)

### Setup stripe account



### Setup project


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