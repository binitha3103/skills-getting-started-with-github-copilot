# Repository instructions

## Build, run, and test

- Install dependencies with `pip install -r requirements.txt`.
- Start the application from the repository root with `python -m uvicorn src.app:app --reload --reload-include 'src/static/*'`. The API docs are at `/docs`; `/` serves the static web app.
- Tests use pytest configuration from `pytest.ini`. Run the suite with `pytest`; run one test with `pytest tests/test_app.py::test_name`.
- No separate lint command is configured in the repository.

## Architecture

- `src/app.py` defines the FastAPI application, API routes, and the activity data. The `activities` dictionary is the in-memory data store: activity names are keys and each value contains its details, capacity, and participant email list. Changes are lost when the process restarts.
- The root route redirects to `src/static/index.html`. The vanilla JavaScript in `src/static/app.js` loads `/activities` and calls the signup and participant-removal endpoints; `styles.css` styles the rendered activity cards and form.
- `.github/steps/` contains the learner-facing exercise instructions. `.github/workflows/` advances the exercise by checking branch and code changes, then posting the next step. Keep these learning materials and their checks aligned when changing exercise requirements.

## Repository conventions

- Preserve the API contract used by the browser: activity names are path identifiers, and `email` is a query parameter for signup and removal. The client URL-encodes activity names and emails.
- Keep activity validation and signup/removal behavior in the FastAPI routes. Missing activities return 404; duplicate signup returns 400; removing an email that is not signed up returns 404.
- Because route handlers mutate module-level in-memory data, tests that add or remove participants must restore or isolate that state so tests do not affect one another.
- Structure backend tests in `tests/` with Arrange–Act–Assert sections, and use pytest fixtures or monkeypatching to isolate application state.
- The app and its static files are rooted under `src/`; run Uvicorn from the repository root using the `src.app:app` import path.
