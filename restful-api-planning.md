# RESTful API Planning: DoorDash-Inspired Meal Browser

This is a small class project inspired by one part of a food-delivery app such as DoorDash: a person browses food choices, picks one, and sends a simple order request. It is not an attempt to recreate DoorDash or describe its actual internal systems. The goal is to practice how a browser, a public API, and a simple Python service could communicate using REST and JSON.

## API Selection

The project will use [TheMealDB](https://www.themealdb.com/api.php), a public recipe API listed in the [Public APIs repository](https://github.com/public-apis/public-apis). TheMealDB returns recipe data such as a meal ID, name, category, area, image, and instructions. In this project, its meals will act as a small stand-in for the food choices a delivery app might display. It is suitable for a beginner project because the documented development key `1` can be used for educational requests and the response is JSON.

In REST terms, each meal is a resource. The API identifies a meal with an ID, such as `52771`. A search or detail request contains the information it needs, such as the meal name or ID, so the API does not need to remember what the browser requested before. This is the stateless idea in a simple form. GET will read meal data. A planned POST request will send an order request to a small Flask backend.

## Endpoint Mapping

| Endpoint | HTTP method | What it does |
| --- | --- | --- |
| `https://www.themealdb.com/api/json/v1/1/search.php?s={mealName}` | GET | Searches the meal resources by name and returns a JSON `meals` list. |
| `https://www.themealdb.com/api/json/v1/1/lookup.php?i={mealId}` | GET | Returns more details for one selected meal. |
| `/orders` | POST | Planned local Flask endpoint that accepts one order request as JSON and returns `201 Created` after accepting it. |

The first two URLs use GET because they only retrieve public data. The planned `/orders` URL represents order requests in the student project. Calling POST with the order JSON is clearer than putting actions such as `createOrder` in the URL.

## Data Flow Diagram

```text
User searches for a meal
        ↓
Page shows “Loading meals...”
        ↓
JavaScript uses Fetch GET to ask TheMealDB for matching meals
        ↓
TheMealDB returns JSON with a meals list
        ↓
Page creates a meal card for each result
        ↓
User selects a meal and completes a simple order form
        ↓
JavaScript turns the form values into JSON for POST /orders
```

The search function will call `fetch()` asynchronously with `Accept: application/json`. The page can stay responsive while the request is running. It will check `response.ok` before reading the JSON. A loading message will be visible during the request, a no-results message will appear when `meals` is empty, and a `catch` block will show a clear message if the network or public API is unavailable.

The returned JSON will become simple cards or table rows showing the meal name, category, area, and image. Meals in the `Vegetarian` category will receive a green category label. Other categories will use the normal label. This keeps the conditional styling easy to recognize without suggesting that one meal is better than another.

## Form Handling

The page will have a search field and a small order form. The search field is required, must contain at least two characters, and will be trimmed before JavaScript adds it to the URL with `encodeURIComponent()`.

The order form will include these fields:

- Selected meal ID: a hidden value filled in when the user chooses a meal.
- Meal name: a read-only value shown to the user.
- Customer name: required text.
- Quantity: required number from 1 through 10.
- Order type: required dropdown with Pickup or Delivery.
- Email: required and checked with the browser email format.
- Notes: optional text limited to 250 characters.

Before a POST request, the browser will check each rule and show an error near the form when something is missing or invalid. A valid form will become a JSON object like this:

```json
{
  "meal_id": "52771",
  "meal_name": "Spicy Arrabiata Penne",
  "customer_name": "Jordan Lee",
  "quantity": 2,
  "order_type": "Pickup",
  "email": "jordan@example.com",
  "notes": "Please pack utensils."
}
```

JavaScript will send that JSON to the future Flask `/orders` endpoint using `Content-Type: application/json`. A `201 Created` response will show a confirmation. Other responses will show an error instead of pretending that the order was accepted.

## Technology Stack

- HTML for the form and meal results.
- CSS for basic layout and the vegetarian category label.
- JavaScript and the browser Fetch API for GET and planned POST requests.
- TheMealDB for the public recipe data.
- Flask and Python for the small future `/orders` service.
- Browser developer tools, `curl`, and Flask's test client or `pytest` for checking requests and responses.
- Git and GitHub for version control and the assignment submission.

## Implementation Steps

1. Create a basic HTML page with a meal-search field and a section for results.
2. Add Fetch GET with a loading message, no-results message, and error message.
3. Turn the returned meal JSON into cards or a table, then add the vegetarian category styling.
4. Add a Select button to copy the chosen meal ID and name into the order form.
5. Add the order fields and local validation before constructing the JSON payload.
6. Create a simple Flask `/orders` endpoint later and test a good request, invalid form input, no search results, and a failed API request.
7. Review the URLs, GET and POST behavior, messages, and documentation before running the project locally.

## Deployment Plan

The first version will run locally because this assignment is about understanding the request flow. The browser page will act as the client, TheMealDB will provide the public food data, and Flask will be the local service that receives order JSON. In a larger app, those pieces could be hosted separately, but the student version keeps them small enough to test and explain clearly.
