# Services


## Product Catalog Service
```
curl -X 'GET' \
  'http://product-catalog-service/get-product?product_id=OLJCESPC7Z' \
  -H 'accept: application/json'


# Result
{
    "products": [
        {
            "id": "OLJCESPC7Z",
            "name": "Sunglasses",
            "description": "Add a modern touch to your outfits with these sleek aviator sunglasses.",
            "picture": "/static/img/products/sunglasses.jpg",
            "priceUsd": {
                "currencyCode": "USD",
                "units": 19,
                "nanos": 990000000
            },
            "categories": ["accessories"]
        },
        {
            "id": "66VCHSJNUP",
            "name": "Tank Top",
            "description": "Perfectly cropped cotton tank, with a scooped neckline.",
            "picture": "/static/img/products/tank-top.jpg",
            "priceUsd": {
                "currencyCode": "USD",
                "units": 18,
                "nanos": 990000000
            },
            "categories": ["clothing", "tops"]
        }
     ]
}
```


## Cart Service
```
curl -X 'GET' \
  'http://cart-service/cart/users/abcde' \
   -H 'accept: application/json'
   
curl -X 'POST' 'http://cart-service/cart/users/abcde' \
  -H 'accept: application/json' \
  -H 'content-type: application/json' \
  -d '{
  "product_id": "L9ECAV7KIM",
  "quantity": 8
}'
```

## Checkout Service
```
curl -X 'POST' \
  'http://checkout-service/checkout' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "user_id": "abcde",
  "user_currency": "USD",
  "address": {
    "street_address": "1600 Amp street",
    "city": "Mountain View",
    "state": "CA",
    "country": "USA",
    "zip_code": "94043"
  },
  "email": "someone@example.com",
  "credit_card": {
    "credit_card_number": "4432-8015-6251-0454",
    "credit_card_cvv": 672,
    "credit_card_expiration_year": 24,
    "credit_card_expiration_month": 1
  }
}'
```


## Payment Service
```
curl -X 'POST' \
  'http://payment-service/charge' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "amount": {
    "currency_code": "USD",
    "units": 245,
    "nanos": 9900000
  },
  "credit_card": {
    "credit_card_number": "4432-8015-6152-0454",
    "credit_card_cvv": 672,
    "credit_card_expiration_year": 2024,
    "credit_card_expiration_month": 1
  }
}'
```


## Login Service

## Shipping Service

## Login Service

## Recommendation Service
