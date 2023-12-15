## Sample Solution
Creating a cart-service using Python with FastAPI, PostgreSQL, Docker, and Docker Compose involves several steps. Here's a detailed guide:

### 1. Setting Up the Python Environment

1. **Create a Virtual Environment**:
   - In your terminal, navigate to your project directory.
   - Run `python -m venv venv` to create a virtual environment named `venv`.
   - Activate the environment:
     - Windows: `venv\Scripts\activate`
     - Linux/macOS: `source venv/bin/activate`

2. **Add Dependencies**:
   - Create a file named `requirements.txt` in your project directory.
   - Add the following dependencies:
     ```
     fastapi
     uvicorn
     psycopg2-binary
     sqlalchemy
     ```

3. **Install Dependencies**:
   - Run `pip install -r requirements.txt` to install the required packages.

### 2. Database Connection and SQL Schema

- **Database Connection Code**: In your main application file (e.g., `main.py`), add the following code to connect to the PostgreSQL database at startup:

```
  CREATE TABLE IF NOT EXISTS cart_items (
      user_id VARCHAR NOT NULL,
      product_id VARCHAR NOT NULL,
      quantity INTEGER NOT NULL,
      PRIMARY KEY (user_id, product_id)
  );
```

```python
# db.py
import os
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base, sessionmaker


DATABASE_URL = os.getenv("DATABASE_URL", "postgresql://user:password@127.0.0.1:5432/cartdb")
# DATABASE_URL = os.getenv("DATABASE_URL", "postgresql://user:password@db:5432/cartdb")

engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

```

- **SQL Schema for User Cart**: Create a file `models.py` and define the schema as follows:
```
# model.py

from sqlalchemy import Column, Integer, String, create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

Base = declarative_base()

class CartItem(Base):
    __tablename__ = 'cart_items'
    user_id = Column(String, primary_key=True, index=True)
    product_id = Column(String, primary_key=True, index=True)
    quantity = Column(Integer)

```

- **Main Service**
```python
from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from sqlalchemy.exc import NoResultFound
from db import SessionLocal, engine
from models import CartItem 
from pydantic import BaseModel

class CartItemRequest(BaseModel):
    product_id: str
    quantity: int

app = FastAPI()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.post("/cart/users/{user_id}")
def add_or_update_cart_item(user_id: str, cart_item: CartItemRequest, db: Session = Depends(get_db)):
    db_item = db.query(CartItem).filter(CartItem.user_id==user_id, CartItem.product_id==cart_item.product_id).first()
    
    if db_item:
        # If the item exists, update the quantity
        db_item.quantity += cart_item.quantity
    else:
        # If the item does not exist, create a new one
        db_item = CartItem(user_id=user_id, product_id=cart_item.product_id, quantity=cart_item.quantity)
        db.add(db_item)
    
    try:
        db.commit()
        db.refresh(db_item)
    except Exception as e:
        db.rollback()
        raise HTTPException(status_code=500, detail=str(e))
    
    return db_item

@app.get("/cart/users/{user_id}")
async def get_cart_items(user_id: str, db: Session = Depends(get_db)):
    cart_items = db.query(CartItem).filter(CartItem.user_id == user_id).all()
    if not cart_items:
        raise HTTPException(status_code=404, detail="No cart items found for this user")

    return [{"product_id": item.product_id, "quantity": item.quantity} for item in cart_items]

@app.delete("/cart/users/{user_id}")
async def delete_cart_items(user_id: str, db: Session = Depends(get_db)):
    result = db.query(CartItem).filter(CartItem.user_id == user_id).delete()
    if result == 0:
        raise HTTPException(status_code=404, detail="No cart items found for this user")

    db.commit()
    return {"msg": "All cart items deleted"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8080)
```

### 4. Docker Configuration

- **Dockerfile for Cart Service**:
  - Create a `Dockerfile` in your project root with the following content:
```
FROM python:3.9

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8080"]
```

- **Docker Compose File**:
  - Create a `docker-compose.yaml` file:
```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - db
    environment:
       - DATABASE_URL=postgresql://user:password@db:5432/cartdb
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: cartdb
    volumes:
      - ./init-db:/docker-entrypoint-initdb.d/ 
    ports:
      - "5432:5432"
```

### 5. Testing the Service

- **Test Case for POST Request**:
  - Create a test file `test_cart.py`.
  - Use Python's `requests` library to send POST requests and validate the response.
