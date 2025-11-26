# Backend Summary: NestJS API

## Environment Configuration

The backend supports environment-specific configuration files:
- `.env.development` for development settings
- `.env.production` for production settings

The application automatically loads the correct file based on the `NODE_ENV` variable. This allows you to manage different settings (such as ports, CORS origins, white list IPs, API keys, etc.) for each environment without changing the code.

## IP Filtering Middleware

This project includes a middleware to restrict access to the API by client IP address. Allowed IPs are configured via the environment variable `ALLOWED_IPS` (comma-separated list).

- The middleware checks the client IP using recommended properties (`req.ip`, `x-forwarded-for`, and `req.socket.remoteAddress`).
- If the IP is not in the allowed list, the request is denied with a 403 Forbidden error.
- To configure allowed IPs, set the `ALLOWED_IPS` variable in your `.env.development` file:

```
ALLOWED_IPS=127.0.0.1,::1
```

## How to Start the Server

Run the following command from the project root:

```bash
cd ./prueba-nestjs
npm i
npm run start:dev
```
This will start the NestJS server in development mode.

## Exposed Routes

- `GET /` — Main route. Responds with a welcome message.
- `GET /health` — Dedicated health check route. Returns status and timestamp.
- `POST /candidates` — Receives `name`, `surname`, and an Excel file. Validates and processes candidate data from the uploaded file.
- `GET /candidates/example-excel` — Downloads a valid example Excel file.
- `GET /api/docs` — Exposes the Swagger API documentation (OpenAPI UI).

## Validations

- **DTO Validation:**
  - `name` and `surname` are required and validated using class-validator decorators.

- **Excel File Validation (Custom Pipe):**
  - File must be present and of type `.xlsx`.
  - Maximum file size: 200KB.
  - Must contain exactly 3 headers: `Seniority`, `Years of experience`, `Availability`.
  - Must contain exactly 3 values, all non-empty.
  - Returns HTTP 400 with descriptive error messages for any validation failure.

## Error Handling for Undefined Routes

Any request to an undefined route will return a controlled 404 error response with the following structure:

```
{
  "statusCode": 404,
  "message": "Route not found",
  "error": "Not Found",
  "timestamp": "<ISO timestamp>"
}
```
This is managed globally using a custom NestJS filter (`NotFoundFilter`) registered in `main.ts`.

## Unit Test Summary

- Controllers, services, DTOs, pipes, and custom filters are covered with Jest unit tests.
- The NotFoundFilter is unit tested to ensure it returns a controlled 404 error response for undefined routes.
- All validation scenarios are tested, including error messages and status codes.
- Configuration modules (`AppModule`, `CandidatesModule`) are tested to ensure controllers and services are properly defined.
- Coverage for business logic files is high (>80%).

## End-to-End (e2e) Test Summary

- e2e tests validate that the application boots up and responds to HTTP requests as expected.
- The root endpoint (`GET /`) is tested for correct status and response.
- The health check endpoint (`GET /health`) is tested for status and timestamp.
- The candidates endpoint (`POST /candidates`) is tested for:
  - Successful creation with valid data and Excel file.
  - Validation error when required parameters (e.g., `surname`) are missing.
- Custom 404 message for undefined routes from a global filter:
  - Verifies POST request to `/health` (unsupported method) returns custom message.
  - Verifies request to an undefined route (`/non-existent-route`) returns custom message.
- These tests indirectly cover the application bootstrap (`main.ts`).

## API Documentation

The backend exposes interactive API documentation using Swagger (OpenAPI). Access it at `/api/docs` to view, test, and explore all available endpoints and their schemas.

## How to Build

Run `npm run build` in the backend folder to generate the production-ready files in the `dist` directory. All necessary static assets are copied automatically.

## TODOs

- Work with Monorepo for this case or Swagger Open Api with YML files.


# Frontend Summary: Angular Standalone App

## Structure and Technologies

- Angular 21 standalone (no NgModule)
- Angular Material for UI
- Reactive Forms for validation
- Internationalization with Transloco (standalone compatible)
- Layout with Flexbox and Material Card

## Internationalization i18n

- Implemented with Transloco, which supports standalone components.
- Language files are located in `public/i18n/` folder.
- Dynamic language switching is supported.
- All translation keys are encapsulated under the parent `app-root` for modularity.
- Created standalone language selector component using Angular Material (`MatSelect`).
- Users can switch between available languages (Spanish and English) dynamically from the UI.

## Main Components

- Candidate form with reactive validations and translated error messages.
- Excel file input with custom validation.
- Redirect for non-existent routes to the main route.
- Styles and comments are in English to maintain best practices.

## Test

- Unit tests implemented for main components and dialogs with Jasmin.
- Coverage > 90%

## How to Run the Frontend

```bash
cd ./candidates-angular-21
npm i
npm start
```
This starts the Angular application in development mode.

## Notes on Standalone and i18n

- Transloco is the community-recommended library for internationalization in Angular standalone.
- ngx-translate does not support pure standalone (Angular 16+).
- The translation key structure allows scalability per component.
- You can view added candidates in a table (standalone component).
- You can delete added candidates, showing an alert message (standalone component).
- An error message is displayed when there is a failure in the backend call (standalone component).

## Environment Configuration

- API URLs and other variables are defined in `src/environments/environment.ts` (development) and `src/environments/environment.prod.ts` (production).

## Documentation

The frontend uses Compodoc to generate interactive documentation for Angular components, services, and configuration. All docs are generated from code and JSDoc comments.

To generate the documentation, run:
`npm run docs:gen`

To serve the documentation locally, run:
`npm run docs:serve`

## How to Build

Run `npm run build` in the frontend folder to generate the production-ready files in the `dist/candidates-angular-21` directory. All assets and translations are included automatically.

## TODOs

- Create interface for the const "environment"
- Responsive???
- Persistence???
- Work with Monorepo for this case or Swagger Open Api with YML files.
