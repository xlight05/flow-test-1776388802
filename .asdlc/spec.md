# Overview

This project is a RESTful API service for managing a personal todo list. It allows users to create, read, update, and delete todo items through a well-defined HTTP interface, with all data persisted reliably in a relational datastore. The system is intended to serve as a backend for web, mobile, or CLI clients that need structured task management.

The target users are application developers who will integrate the API into client applications, and end users (via those clients) who need a simple, dependable way to track tasks. The API emphasizes correctness, predictable behavior, clear error reporting, and durable storage.

The high-level approach is to expose a small set of resource-oriented endpoints for todo items, enforce validation on all inputs, persist data durably, and support standard operational concerns such as health checks, logging, and configurability across environments.

# Capabilities

## Todo Item Management
- Users can create a new todo item with a title (required) and optional description, due date, and priority.
- Users can retrieve a single todo item by its unique identifier.
- Users can list all todo items, with support for pagination (limit and offset or cursor).
- Users can filter the list by completion status, priority, and due-date range.
- Users can sort the list by creation date, due date, or priority, in ascending or descending order.
- Users can update any mutable field of an existing todo item (title, description, due date, priority, completion status).
- Users can mark a todo item as complete or incomplete.
- Users can delete a todo item by its identifier.
- Deletion returns a clear response when the item did not exist.

## Data Model and Validation
- Each todo item has a unique identifier, title, optional description, completion status, optional due date, priority, creation timestamp, and last-updated timestamp.
- Title must be non-empty and limited to a reasonable maximum length (e.g., 200 characters).
- Description, if present, is limited to a reasonable maximum length (e.g., 2000 characters).
- Priority is restricted to a defined set of values (e.g., low, medium, high).
- Due date, if present, must be a valid ISO 8601 timestamp.
- Timestamps are automatically set by the system and cannot be overwritten by clients.
- Invalid input returns a structured validation error describing each offending field.

## API Behavior
- All endpoints accept and return JSON.
- The API uses standard HTTP status codes (200, 201, 204, 400, 404, 409, 500).
- Error responses follow a consistent schema including an error code, message, and optional field-level details.
- The API is versioned via a URL prefix (e.g., `/v1`).
- Requests with malformed JSON return a 400 response with a clear message.
- Unknown routes return a 404 response in the standard error format.

## Persistence
- All todo items are stored durably in a relational database.
- Data survives service restarts without loss.
- Database schema is managed through versioned migrations that can be applied automatically or on demand.
- Concurrent updates to the same item are handled safely without data corruption.

## Configuration and Environments
- Database connection details and service port are configurable via environment variables.
- The service supports distinct configurations for development, staging, and production.
- Secrets are never hard-coded and are loaded from the environment.

## Observability and Operations
- The service exposes a health-check endpoint reporting overall service status.
- The service exposes a readiness endpoint that verifies database connectivity.
- All requests are logged with method, path, status code, and latency.
- Errors are logged with enough context to diagnose the issue without exposing sensitive data.

## Security and Reliability
- Input is validated and sanitized to prevent injection attacks.
- The API enforces a maximum request body size to prevent abuse.
- The service returns consistent, non-sensitive error messages to clients while logging full details internally.
- The service handles database outages gracefully, returning 503 responses rather than crashing.
- Graceful shutdown ensures in-flight requests complete before the process exits.

## Performance
- List endpoints respond within 200 ms under nominal load for datasets up to 100,000 items.
- The service supports at least 100 concurrent requests without degradation under typical hardware.
- Database queries for lookups and list filters are backed by appropriate indexes.

## Documentation and Testing
- The API is documented with a machine-readable specification (e.g., OpenAPI) describing every endpoint, request, and response.
- Example requests and responses are provided for each endpoint.
- Automated tests cover create, read, update, delete, validation, and error paths.
