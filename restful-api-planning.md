# RESTful API Planning: Meal Finder and Favorite Planner

## API Selection

This project will use [TheMealDB](https://www.themealdb.com/api.php), a public recipe API listed in the [Public APIs repository](https://github.com/public-apis/public-apis). It is a good fit because a user can search meals by name and receive JSON records with a meal ID, name, category, area, image, and instructions. The project will use the API's development key `1`, which TheMealDB documents for educational use.

The main resource is a meal. Each meal has its own identifier, such as `52771`, so the client can request or save a specific meal without depending on a previous request. This follows REST's resource-identification idea. The public API is also stateless because each GET request contains the search term or meal ID it needs. Standard GET requests read meal data without changing it, while a planned POST request will send a selected meal plan to a separate Python backend.

## Endpoint Mapping

| Endpoint | HTTP method | Purpose and data |
| --- | --- | --- |
| `https://www.themealdb.com/api/json/v1/1/search.php?s={mealName}` | GET | Searches meals by name and returns a JSON object with a `meals` array. |
| `https://www.themealdb.com/api/json/v1/1/lookup.php?i={mealId}` | GET | Returns the complete details for one selected meal resource. |
| `/api/favorites` | POST | Planned local Python endpoint that accepts a JSON meal-plan record and returns `201 Created` when saved. |

The first two endpoints read public resources. The local `/api/favorites` endpoint represents a favorite or planned meal resource. Every request will carry the information needed to process it, including the search value, meal ID, or JSON body, so the server does not need to remember a previous client session.

## Data Flow Diagram

```text
User enters a meal name
        ↓
Browser validates the search text and shows “Loading meals..."
        ↓
JavaScript calls fetch(search URL, { headers: { Accept: "application/json" } })
        ↓
TheMealDB returns JSON with a meals array
        ↓
Client checks response.ok, reads JSON, and maps each meal to a card or table row
        ↓
Vegetarian meals receive a green category label; other meals keep the normal label
        ↓
No matching meals or a network failure replaces the loading message with a clear error message
```

The fetch request will be asynchronous, so the page remains usable while it waits. If `response.ok` is false, the client will display an API error instead of trying to render a response that may not contain meal data. A `catch` block will handle connection failures. Each rendered result will include a button that selects its meal ID for the favorite form.

## Form Handling

The search form will contain a required meal-name text field with a minimum of two characters. The value will be trimmed and passed through `encodeURIComponent()` before it becomes the `s` query parameter in the GET request.

A second form will plan a selected meal for the user. It will include a hidden `meal_id` field, a read-only `meal_name` field, a required date field, a required email field, and an optional notes field limited to 250 characters. The browser will reject an empty meal selection, a missing date, an invalid email format, or notes longer than the limit before sending anything. After validation, JavaScript will create this JSON payload and POST it to the future Python backend:

```json
{
  "meal_id": "52771",
  "meal_name": "Spicy Arrabiata Penne",
  "planned_date": "2026-12-01",
  "email": "student@example.com",
  "notes": "Make extra for lunch."
}
```

The POST will use `Content-Type: application/json` and `Accept: application/json`. The client will show a confirmation for a `201 Created` response and a useful error message for any other response.

## Technology Stack

- HTML, CSS, and JavaScript for the client page and conditional styling.
- Browser Fetch API for asynchronous JSON requests.
- TheMealDB as the public REST API.
- Flask and Python for the planned `/api/favorites` backend.
- Browser developer tools, `curl`, and Flask's test client or `pytest` for testing.
- Git and GitHub for version control and the public planning repository.

## Implementation Steps

1. Create the project folder and a simple HTML, CSS, and JavaScript client page.
2. Add the meal-name search form and implement asynchronous Fetch GET calls with a visible loading message and error message.
3. Render returned meal JSON as a dynamic list or table, including the meal name, category, area, and selection button.
4. Apply a separate CSS class to vegetarian category labels so the conditional styling is easy to see.
5. Add the favorite-planning form, its local validation rules, and JSON construction for the planned POST contract.
6. Add a small Flask `/api/favorites` endpoint later, then test successful POSTs, invalid form input, no-result searches, and network or API errors.
7. Review the resource names, HTTP methods, messages, and README before serving the client locally and publishing the project documentation to GitHub.

## Deployment Plan

During development, the client will be served locally so Fetch behavior can be inspected in the browser network tools. After the Python endpoint is implemented and tested, the client files can be deployed as a static site while the Flask service is deployed separately with its base URL configured in the client. This keeps the client responsible for presentation and the Python service responsible for accepting and storing favorite meal plans.

