# Sample Solution
Sure, I can help you with that! Here's a step-by-step guide on how to create a cart-service in Python using FastAPI, package it as a Docker image, and run it with Docker Compose.

### Step 1: Set Up a Virtual Environment and Install Dependencies

1. Create a directory for your project and navigate to it:

```bash
mkdir cart-service
cd cart-service
```

2. Create a virtual environment:

```bash
python -m venv venv
```

3. Activate the virtual environment:

On Windows:
```bash
venv\Scripts\activate
```

On macOS and Linux:
```bash
source venv/bin/activate
```

4. Create a `requirements.txt` file with the necessary dependencies:

```plaintext
fastapi==0.68.0
uvicorn==0.15.0
```

5. Install the dependencies using `pip`:

```bash
pip install -r requirements.txt
```

### Step 2: Initialize an Empty Cart Item Cache

In your FastAPI app, you can use a Python dictionary as an in-memory cache to store cart items. Initialize it in your FastAPI app as follows:

```python
from fastapi import FastAPI

app = FastAPI()

# In-memory cart item cache
cart_items = {}
```

### Step 3: Implement the HTTP API

Now, let's implement the HTTP API for adding, getting, and deleting cart items.

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

# In-memory cart item cache
cart_items = {}

class CartItem(BaseModel):
    product_id: str
    quantity: int

# Add cart items
@app.post("/cart/{user_id}")
async def add_cart_item(user_id: str, cart_item: CartItem):
    if user_id not in cart_items:
        cart_items[user_id] = []
    
    cart_items[user_id].append(cart_item)
    return {"message": "Cart item added successfully"}

# Get cart items
@app.get("/cart/{user_id}")
async def get_cart_items(user_id: str):
    if user_id not in cart_items:
        return {"message": "Cart is empty for this user"}
    
    return cart_items[user_id]

# Delete cart items
@app.delete("/cart/{user_id}")
async def delete_cart_items(user_id: str):
    if user_id in cart_items:
        del cart_items[user_id]
        return {"message": "Cart items deleted successfully"}
    
    raise HTTPException(status_code=404, detail="Cart not found")
```

### Step 4: Create a Dockerfile

Create a `Dockerfile` in your project directory to package your service as a Docker image:

```Dockerfile
# Use the official Python image as a parent image
FROM python:3.9

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME cart-service

# Run app.py when the container launches
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```

### Step 5: Create a Docker Compose Configuration

Create a `docker-compose.yml` file to define how your service should run alongside any required dependencies:

```yaml
version: '3'
services:
  cart-service:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8080:80"
```

### Step 6: Build and Run the Docker Containers

Build the Docker image for your cart-service:

```bash
docker-compose build
```

Run the Docker containers:

```bash
docker-compose up
```

Your cart-service should now be running and accessible at `http://cart-service:8080`.

You can use the provided HTTP requests to interact with your cart-service as mentioned in your question. Make sure to replace `user_id` and other parameters with actual values when making requests.
