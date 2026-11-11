# RESTful API Planning: Meal Finder and Favorite Planner

The complete planning document is available in [restful-api-planning.md](restful-api-planning.md). This README also summarizes the required plan sections for quick review.

## API Selection

The project will consume TheMealDB, a public recipe API from the Public APIs repository. Meals are resources identified by their meal IDs. Search and detail requests use GET, carry their own query value or ID, and do not depend on an earlier client session.

## Endpoint Mapping

- `GET /api/json/v1/1/search.php?s={mealName}` searches meal resources by name.
- `GET /api/json/v1/1/lookup.php?i={mealId}` retrieves one meal's details.
- Planned `POST /api/favorites` sends a selected meal plan as JSON to a Flask backend and receives `201 Created` when saved.

## Data Flow Diagram

```text
Search form → loading message → asynchronous Fetch GET → JSON response
→ dynamic meal cards → vegetarian label styling
→ clear no-result or error message when needed
```

The client will send `Accept: application/json`, check `response.ok`, use a `catch` block for connection problems, and map the `meals` array into visible cards or rows.

## Form Handling

The search field is required and must contain at least two characters. The favorite-plan form will require a selected meal ID, meal date, and valid email, while notes remain optional and limited to 250 characters. After local validation, JavaScript will convert the values into JSON and POST them to the future Flask endpoint with `Content-Type: application/json`.

## Technology Stack

HTML, CSS, JavaScript, the Fetch API, TheMealDB, Flask, Python, browser developer tools, `curl`, and Flask tests or `pytest` will support the project.

## Implementation Steps

1. Create the client page and search form.
2. Add Fetch GET with loading and error states.
3. Render returned JSON dynamically and style vegetarian labels.
4. Add the favorite-plan form, validation, and JSON POST contract.
5. Build and test the Flask endpoint, then review and deploy the client.

## Deployment Plan

The client will first run locally for browser testing. After the Flask endpoint is available, the static client and Python service can be deployed separately, keeping presentation in the client and saved favorite plans in the backend.
