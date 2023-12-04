# Sample Solution

Creating a cart-service using FastAPI, packaging it as a Docker image, and running it with Docker Compose involves several steps. Let's break down the process step by step:

### Step 1: Setup Cart-Service in a Virtual Environment

1. Create a new directory for your project and navigate to it in your terminal.

2. Create a virtual environment (assuming you have Python installed) to isolate your project dependencies:

```bash
python -m venv venv
```

3. Activate the virtual environment:

   - On Windows:

     ```bash
     venv\Scripts\activate
     ```

   - On macOS and Linux:

     ```bash
     source venv/bin/activate
     ```

4. Inside your virtual environment, install the required dependencies and FastAPI:

```bash
pip install fastapi uvicorn
```

5. Create a `requirements.txt` file to keep track of your project dependencies:

```bash
pip freeze > requirements.txt
```

### Step 2: Initialize Empty Cart Item Cache

In your FastAPI app (let's call it `main.py`), initialize an empty cart item cache at startup:

```python
from fastapi import FastAPI
from typing import Dict

app = FastAPI()

# In-memory cart item cache
cart_cache: Dict[str, Dict[str, int]] = {}


@app.on_event("startup")
async def startup_event():
    # Initialize an empty cart for each user on startup
    # Example: cart_cache["abcde"] = {"L9ECAV7KIM": 8}
    pass
```

### Step 3: Implement HTTP API for Cart Service

In the same `main.py` file, implement the HTTP API to add, get, and delete items from the cart service:

```python
from fastapi import HTTPException

# ...

# Add items to the cart
@app.post("/cart/{user_id}/{product_id}")
async def add_to_cart(user_id: str, product_id: str, quantity: int):
    if user_id not in cart_cache:
        cart_cache[user_id] = {}
    
    if product_id in cart_cache[user_id]:
        cart_cache[user_id][product_id] += quantity
    else:
        cart_cache[user_id][product_id] = quantity
    return {"message": "Item added to cart successfully"}

# Get items from the cart
@app.get("/cart/{user_id}")
async def get_cart(user_id: str):
    if user_id not in cart_cache:
        raise HTTPException(status_code=404, detail="Cart not found")
    
    return cart_cache[user_id]

# Delete items from the cart
@app.delete("/cart/{user_id}")
async def delete_cart(user_id: str):
    if user_id not in cart_cache:
        raise HTTPException(status_code=404, detail="Cart not found")
    
    del cart_cache[user_id]
    return {"message": "Cart deleted successfully"}
```

### Step 4: Create a Dockerfile

Create a `Dockerfile` in the project directory to package the service into a Docker image:

```Dockerfile
# Use the official Python image as the base image
FROM python:3.9

# Set the working directory in the container
WORKDIR /app

# Copy the requirements.txt file into the container
COPY requirements.txt .

# Install project dependencies
RUN pip install -r requirements.txt

# Copy the entire project directory into the container
COPY . .

# Expose the port your FastAPI app will run on (default is 80)
EXPOSE 80

# Command to run the FastAPI app
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```

### Step 5: Create a Docker Compose File

Create a `docker-compose.yaml` file to define and run your Docker containers:

```yaml
version: "3"
services:
  cart-service:
    build: .
    ports:
      - "8080:80"
```

### Step 6: Build and Run the Docker Containers

In your project directory, build and run the Docker containers using Docker Compose:

```bash
docker-compose up --build
```

Now, your cart-service should be up and running in a Docker container, exposed on port 8080. You can use the `curl` commands you provided to interact with the service.

This is a basic setup, and you can enhance it further by adding features like error handling, data validation, and more, depending on your specific requirements.
