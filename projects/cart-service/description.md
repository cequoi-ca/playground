# Cart Service

## Python Implementation
```
mkdir -p cart-service
cd cart-service
python3 -m venv venv
source venv/bin/sctivate
```

## Prompt
```
Implement a cart-service service in python using the python fast API library and packed as a docker image to run with docker compose.  
The cart-service service should cach cart items in memory.

- Explain how to setup the cart-service in python as a virtual environment, and add required dependencies in a requirements.txt file.
- Explain how to initialize an empty cart item cache at startup.
- Explain how to implement the http api to add, get, delete items from the cart-service.
- Explain how to create a docker file to package the service, and create a docker-compose.yaml file to run the service.

The cart-service should implement a http API to add, get and delete cart items as.

### Add cart items 
curl -X 'POST' 'http://cart-service:8080/cart/user_id/abcde' \
  -H 'accept: application/json' \
  -H 'content-type: application/json' \
  -d '{
  "product_id": "L9ECAV7KIM",
  "quantity": 8
}'

### GET cart items 
curl -X 'GET' 'http://cart-service:8080/cart/user_id/abcde' \
  -H 'accept: application/json'

### DELETE cart items 
curl -X 'DELETE' 'http://cart-service:8080/cart/user_id/abcde' \
  -H 'accept: application/json'
