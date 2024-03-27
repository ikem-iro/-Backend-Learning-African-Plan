# Path Parameters:

## What are path parameters in FASTAPI?

- Path parameters are variables parts of a URL path that are typically used to point to a specific resource within a collection, such as a user identified by ID.

## How are path parameters defined in FASTAPI route declaration

- Path parameters are defined within curly braces {} in the URL path. You can then use them as function arguments within your route handler function.

```
- GET /users/{id}
```

## Can path parameters have default values? If yes, how can they be set?

- No, they cannot. Path parameters in FastAPI are inherently required because they form part of the URL structure. The default value for path parameters in FastAPI is not directly supported. Path parameters are always required as part of the path and cannot have default values. Even if a default value is set or declared with None, it does not affect the requirement for the path parameter to be included in the URL path.

## Provide an example of a FastAPI route with path parameters.

```python
@app.get('/users/{item_id}')
async def get_item(item_id)
    pass
```

# Query Parameters:

## What are query parameters in FastAPI?

- Query parameters are key-value pairs appended to the URL after a question mark (?). They provide optional details or filters for the request.

## How are query parameters defined in FastAPI route declarations?

- Query parameters are not directly defined in the path itself. You use the fastapi.Query parameter decorator within your function arguments to access them.

```python
fake_db_items = [
    { "item_name" : "foo" },
    { "item_name" : "bar" },
    { "item_name" : "bars" }
    
]

@app.get("/items/")
async def get_items(skip: int = 0, limit: int = 10):  # Query parameters with defaults
    # Logic to retrieve users with pagination (offset: skip, limit)
    return fake_db_items[skip : skip + limit]
```

## What is the difference between path parameters and query parameters?

- Path parameters are part of the URL structure and are mandatory. They are used to identify specific resources.
- Query parameters are optional and provide additional details or filters for the request.

## Can query parameters have default values? If yes, how can they be set?
- Yes, you can set default values for query parameters using the default argument of the fastapi.Query decorator. To set default values for query parameters, you can assign the desired default value directly in the parameter declaration. For example, in FastAPI, you can declare a query parameter like this: skip: int = 0 to set the default value of skip to 0. This allows the query parameter to be optional with a default value if not provided in the URL.


## Provide an example of a FastAPI route with query parameters.
```python
@app.get("/products/")
async def get_products(category: str = None, sort: str = "asc"): 
    return filtered_products
```


# Combining Path and Query Parameters

## Can a FastAPI route have both path parameters and query parameters? If yes, provide an example.
- Yes, a FastAPI route can have both path parameters and query parameters. This allows you to combine resource identification with filtering or pagination options. Here is how they could be set:

```python

from fastapi import FastAPI

app = FastAPI()

@app.get("/users/{user_id}/items/{item_id}")
async def read_user_item(user_id: int, item_id: str, q: str = None, short: bool = False):
item = {"item_id": item_id, "owner_id": user_id}
if q:
item.update({"q": q})
if not short:
item.update({"description": "This is an amazing item that has a long description"})
return item
```
## What is the order of precedence if a parameter is defined both in the path and as a query parameter?
- If a parameter name appears in both the path and as a query parameter, the path parameter takes precedence. The value from the URL path will be used, and the query parameter value will be ignored.


# Data Types and Validation

## How does FastAPI handle data types and validation for path and query parameters?
### FastAPI utilizes Pydantic for data validation in path and query parameters. Here's how it works:
- Type annotations: You specify expected data types using Python type hints within your function arguments when declaring them with fastapi.Path or fastapi.Query.
- Pydantic validation: When a request arrives, FastAPI uses Pydantic to validate the provided values against the declared types. This ensures data integrity and type safety within your API.

## What are some common data types that can be used for path and query parameters?
### Several common data types are suitable for path and query parameters:
- Basic types: int, str, float, bool
- Pydantic models: You can create custom Pydantic models to represent complex data structures for parameters.
- Enumeration types: Define custom enumeration classes using Enum from Pydantic to restrict parameters to specific allowed values.

## How can you enforce validation rules on path and query parameters in FastAPI?
- Pydantic allows you to define complex validation rules within your data models. By using these models as types for path or query parameters, FastAPI will automatically validate incoming data against those rules.
- Here is an example:
```python
from fastapi import FastAPI
from pydantic import BaseModel, EmailStr

class User(BaseModel):
username: str
email: EmailStr # Ensures valid email format

app = FastAPI()

@app.post("/users")
async def create_user(user: User):
    pass
```

# Usage and Benefits

## In what scenarios would you use path parameters over query parameters, and vice versa? 
### Use path parameters when:
- Identifying a specific resource:
```python
      @app.get("/users/{user_id}")
      async def get_user(user_id: int):
          # Get user details based on user_id

      """The parameter is a core part of the URL structure and always required."""
```
- Providing optional filters or configuration:
```python
      @app.get("/products/")
      async def get_products(category: str = None, sort: str = "asc"):
          # Filter products based on category and sort order

       """The parameter can be omitted for a basic request."""
```

## What are the benefits of using path and query parameters in API design?

- Clarity and Readability: Path parameters make URLs more descriptive, reflecting the resource structure. Query parameters help differentiate filtering options.
- Flexibility: Path parameters allow for targeted resource access, while query parameters provide control over optional functionalities.
- Standardization: Path parameters are often used for core resources, aligning with RESTful API conventions.

### Real-world Use Cases:
#### E-commerce API:
- Path parameter: /products/{product_id} to get details of a specific product.
- Query parameters: /products?category=electronics&sort=price to filter and sort products.
#### Social Media API:
- Path parameter: /users/{user_id} to access a user's profile.
- Query parameter: /posts?limit=20 to retrieve the latest 20 posts.

# Error Handling

## How does FastAPI handle errors related to missing or invalid path/query parameters?
- FastAPI automatically raises exceptions for missing or invalid parameters. These exceptions are typically HTTPException with appropriate status codes (e.g., 400 Bad Request for invalid data).

## Can you customize error responses for cases where required parameters are missing or validation fails?
- Yes, you can customize error responses using exception handlers in FastAPI. These handlers allow you to define specific responses for different exception types.
