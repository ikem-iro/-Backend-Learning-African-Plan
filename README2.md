## Question 1

- Write a FastAPI route that accepts a path parameter for user identification (user_id) and returns the user's profile information.
- Implement error handling for the case when the user_id path parameter is missing.

```python
    user_profiles = {
        1: {
            "name": "Amara",
            "age": 25,
            "city": "New York",
        },
        2: {
            "name": "Miracle",
            "age": 30,
            "city": "London",
        },
        3: {
            "name": "Charlie",
            "age": 35,
            "city": "Paris",
        }
    }
    @app.get('/user/{user_id}')
    async def get_user(user_id: int):
        """
        Retrieves a user by their ID.

        Parameters:
            user_id (int): The ID of the user to retrieve.

        Returns:
            dict: A dictionary containing the user ID if successful.
                - user_id (int): The ID of the user.
            dict: A dictionary containing an error message if an exception occurs.
                - error (str): The error message.
        """
        try:
            if user_id not in user_profiles:
                raise HTTPException(status_code=404, detail="User not found")
            return {"user_id": user_id, "user_detais": user_profiles[user_id]}
        except HTTPException as e:
            return {'error': {e}}
```

## Question 2

- Create a FastAPI route that accepts query parameters for filtering a list of products by category and price range.
- Implement default values for the query parameters (category defaulting to 'all' and price_range defaulting to a specific range).

```python
    products = [
    {"name": "Widget A", "category": "Electronics", "price": 50},
    {"name": "Widget B", "category": "Fashion", "price": 30},
    {"name": "Widget C", "category": "Home", "price": 20},
    ]

    @app.get('/products')
    async def get_products(category:str = "all", price_range: str = "10-20"):
        """
        Retrieves a list of products based on the provided query parameters.

        Parameters:
            category (str, optional): The category of the products to retrieve. Defaults to "all".
            price_range (str, optional): The price range of the products to retrieve. Defaults to "20-50".

        Returns:
            list: A list of dictionaries containing the product information if successful.
                - name (str): The name of the product.
                - category (str): The category of the product.
                - price (int): The price of the product.
            dict: A dictionary containing an error message if an exception occurs.
                - error (str): The error message.
        """
        try:
            min_price, max_price = map(int, price_range.split('-'))
            if min_price > max_price:
                raise HTTPException(status_code=400, detail="Invalid price range")
            if min_price <= 0 or max_price <= 0:
                raise HTTPException(status_code=400, detail="Invalid price range")
            filtered_products = [product for product in products if (category == "all" or product["category"] == category.capitalize()) and min_price <= product["price"] <= max_price]
            return {"filtered products": filtered_products}
        except HTTPException as e:
            return {'error': {e}}


```

## Question 3

- Write a FastAPI route that accepts a path parameter for city (city_id) and query parameters for filtering restaurants by cuisine type (cuisine) and rating
  (min_rating).
- Ensure that the city ID is a path parameter while cuisine type and minimum rating are query parameters.

```python
restaurants_data: list[dict[str, str]] = [
    {"id": "New York", "name": "Delicious Bites", "cuisine": "Italian", "rating": 4.5},
    {"id": "Los Angeles", "name": "Spicy Wok", "cuisine": "Chinese", "rating": 3.8},
    {"id": "Los Angeles", "name": "Tandoori Flavors", "cuisine": "Indian", "rating": 4.2},
    {"id": "New York", "name": "Delicious Bites", "cuisine": "Italian", "rating": 4.0},
    {"id": "Los Angeles", "name": "Uncle Roger", "cuisine": "Chinese", "rating": 4.5},
]

@app.get("/restaurants/{city_id}")
async def get_restaurants(city_id: str, cuisine: str = "all", rating: str = "1-5"):
    """
    A coroutine that retrieves restaurants based on city ID, cuisine, and rating.

    Parameters:
    - city_id: str - The ID of the city to retrieve restaurants from.
    - cuisine: str - The type of cuisine to filter the restaurants by. Defaults to "all".
    - rating: str - The rating range to filter the restaurants by. Defaults to "1-5".

    Returns:
    - dict: A dictionary containing the city ID and a list of filtered restaurants.
    """

    min_rating, max_rating = map(float, rating.split('-'))
    if city_id not in [restaurant["id"] for restaurant in restaurants_data]:
        raise HTTPException(status_code=404, detail="City not found")
    filtered_restaurants = []

    for restaurant in restaurants_data:
        if restaurant["id"] == city_id:
            if (cuisine == "all" or restaurant["cuisine"] == cuisine) and min_rating <= restaurant["rating"] <= max_rating:
                filtered_restaurants.append(restaurant)

    return {"city_id": city_id, "restaurants": filtered_restaurants}

```

## Question 4

- Modify an existing FastAPI route that accepts a path parameter for user_id to ensure that user_id is an integer and greater than zero.
- Add validation to a query parameter start_date to ensure it is a valid date format.

```python
users_data: dict[int, dict[str, str]] = {
   1: {"name": "Amara", "email": "amara@example.com", "start_date": "2022-01-15"},
    2: {"name": "Miracle", "email": "miracle@example.com", "start_date": "2022-03-20"},
    3: {"name": "Charlie", "email": "charlie@example.com", "start_date": "2022-02-10"}
}
@app.get('/users/{user_id}')
async def get_user(start_date: str, user_id: int = Path(gt = 0)):
    """
    A function to get user details based on user_id and start_date.

    Parameters:
    - start_date: str, the start date in the format '%Y-%m-%d'
    - user_id: int, the user ID

    Returns:
    - Dictionary: {"user_id": int, "user_details": dict}
    - If an exception occurs, returns a dictionary with the error message.
    """
    try:
        if not datetime.strptime(start_date, '%Y-%m-%d'):
            raise HTTPException(status_code=400, detail="Invalid date format")
        if user_id not in users_data:
            raise HTTPException(status_code=404, detail="User not found")
        if start_date != users_data[user_id]["start_date"]:
            raise HTTPException(status_code=400, detail="Invalid start date")
        return {"user_id": user_id, "user_details": users_data[user_id]}
    except HTTPException as e:
        return{'error': {e}}

```

## Question 5

- Analyze a given FastAPI application and identify instances where using path parameters would be more appropriate than query parameters, and vice versa.
-  Discuss the benefits of using path and query parameters in the provided FastAPI application and how it enhances API design.
```txt
- Path parameters are part of the URL path. They are commonly used to refer to a specific resource within a collection, such as a specific user in a list of users. For example, in the URL https://api.example.com/users/123, 123 is a path parameter that most likely corresponds to a person with the ID 123. Path parameters are ideal for: 
- Identifying a specific resource: Path parameters are very useful for pointing to a specific resource within a collection.
- Creating intuitive URLs: When utilized correctly, path parameters can make your API URLs more logical and understandable.

-Query parameters, which are attached to the URL with a?, are commonly used to filter, sort, or paginate collections. For example, the URL https://api.example.com/users?sort=desc&page=2 has query parameters that most likely sort the users in descending order and request the second page of users. Query parameters are ideal for:
-Filtering & Sorting: Query parameters are ideal for filtering a set of resources or sorting them in a specific order.
-Pagination: If you have a large number of resources and want to divide them into smaller parts, query parameters can be used to request individual pages.
-Optional parameters: If a parameter is optional, it may be used as a query parameter. This means that if the parameter is missing, the server can simply provide a default value.

- Both path parameters and query parameters have their uses, and a well-designed API would most likely employ both. The trick is to use them correctly: path parameters for mandatory values that identify resources, and query parameters for optional values that affect the server's answer. By successfully utilizing both sorts of parameters, you can design an API that is flexible, straightforward, and simple to use.
```