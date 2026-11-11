# RESTful API Planning: DoorDash-Inspired Meal Browser

This class plan imagines a small food-browsing and order-request page inspired by the familiar flow of a delivery app such as DoorDash. It does not claim to recreate DoorDash or describe its real internal systems. The full plan is in [restful-api-planning.md](restful-api-planning.md).

## API Selection

The plan uses TheMealDB, a public recipe API from the Public APIs list. Meal IDs identify the data resources. GET requests search for or look up meals, and a future Flask POST endpoint will accept a simple order request. Each request includes the search value, ID, or JSON body it needs, which demonstrates stateless requests.

## Endpoint Mapping

- `GET /api/json/v1/1/search.php?s={mealName}` finds matching meals.
- `GET /api/json/v1/1/lookup.php?i={mealId}` finds one selected meal.
- Planned `POST /orders` sends one order JSON object to Flask and expects `201 Created`.

## Data Flow Diagram

```text
Search form → loading message → Fetch GET → JSON meals list
→ dynamic meal cards → user selects one → order form JSON → POST /orders
```

Fetch will use `Accept: application/json`, check `response.ok`, and show a loading, no-results, or error message as appropriate. Meal JSON becomes cards or rows. Vegetarian meals get a green category label while other meals keep the normal style.

## Form Handling

The search needs at least two characters. The order form collects a selected meal ID and name, customer name, quantity from 1 through 10, pickup or delivery selection, email, and optional notes limited to 250 characters. JavaScript validates the values locally, then sends a JSON request with `Content-Type: application/json` to the planned Flask endpoint.

## Technology Stack

HTML, CSS, JavaScript, Fetch, TheMealDB, Flask, Python, browser developer tools, `curl`, Flask tests or `pytest`, Git, and GitHub will be used.

## Implementation Steps

1. Build a basic page and meal search form.
2. Add Fetch GET with loading and error states.
3. Render JSON meal cards and vegetarian styling.
4. Add meal selection, order controls, validation, and JSON construction.
5. Build and test the simple Flask POST endpoint, then run locally and review the documentation.

## Deployment Plan

The student version will run locally so the request flow can be tested clearly. The browser page, public recipe API, and small Flask service are separate pieces, similar to the pieces a larger delivery app could connect, but at a scale appropriate for learning.
