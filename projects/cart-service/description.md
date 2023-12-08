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
Implement a cart-service service in python using the python fast API library with a postgres database, both packed as docker images to run with docker compose.  

- Explain how to setup the cart-service in python with a virtual environment, and add required dependencies in a requirements.txt file.
- Write code to connect to the database at startup.
- Write the SQL  schema for a user cart.
- Implement the http API in python to add, get, delete items from the cart-service and update the user cart in database.
- Explain how to create a docker file to package the service, and create a docker-compose.yaml file withe service and postgresql, to run the service with database.
- Write a test case to verify that sending a POST request with {"product_id": "L9ECAV7KIM","quantity": 8} twice will update the quantity in cart to 16
- The project is using VScode, so VScode tools and extension can be recommended
- generate complete source files in python with comments.

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
