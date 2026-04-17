# Overview

This project adds a bulk delete capability to an existing todo management system, enabling clients to delete multiple todo items in a single request by providing a list of todo IDs. The goal is to reduce the number of round-trips required when users need to clear or remove several todos at once, improving both client-side efficiency and user experience.

The target users are API consumers and front-end applications that already interact with the todo service and need to support multi-select deletion flows (e.g., checkbox-based list management). The approach is to introduce a single new endpoint that accepts a collection of identifiers, validates them, performs the deletions atomically where possible, and returns a clear per-item result summary.

# Capabilities

## Bulk Delete Endpoint

- The system shall expose a dedicated endpoint for deleting multiple todos in a single request.
- The endpoint shall accept a list of todo IDs as input.
- The endpoint shall support deleting at least 100 todo IDs in a single request.
- The endpoint shall reject requests exceeding the maximum allowed batch size with a clear error message.
- The endpoint shall reject requests with an empty ID list and return a validation error.
- The endpoint shall reject requests with duplicate IDs or silently de-duplicate them, documenting the chosen behavior.
- The endpoint shall validate that each provided ID conforms to the expected identifier format.

## Deletion Behavior

- The system shall permanently remove todos whose IDs are provided and exist.
- The system shall skip IDs that do not correspond to an existing todo without failing the entire request.
- The system shall only allow a user to delete todos that they own or are authorized to delete.
- The system shall not delete any todos that the requester is not authorized to delete, even if other IDs in the batch are authorized.
- The system shall ensure that a partial failure (e.g., database issue on one item) does not leave the data in an inconsistent state.

## Response and Reporting

- The endpoint shall return a summary indicating how many todos were successfully deleted.
- The endpoint shall return a list of IDs that were successfully deleted.
- The endpoint shall return a list of IDs that were not found.
- The endpoint shall return a list of IDs that failed due to authorization errors.
- The endpoint shall return an appropriate success status code when at least one deletion succeeds and all inputs were valid.
- The endpoint shall return an appropriate error status code when the request itself is invalid (malformed body, missing IDs, etc.).

## Authentication and Authorization

- The endpoint shall require the caller to be authenticated.
- The endpoint shall enforce the same authorization rules used by the single-item delete endpoint.
- The system shall log each bulk delete operation with the requester's identity and the list of affected IDs for audit purposes.

## Performance and Reliability

- The endpoint shall complete a bulk delete of 100 items within a reasonable response time under normal load.
- The endpoint shall be idempotent: repeating the same request shall not cause errors for IDs already deleted.
- The system shall protect against abuse by applying rate limits consistent with other write endpoints.
- The endpoint shall handle concurrent bulk delete requests safely without data corruption.

## Documentation and Compatibility

- The new endpoint shall be documented in the existing API documentation, including request schema, response schema, and example payloads.
- The addition of this endpoint shall not alter the behavior of any existing todo endpoints.
- Error responses shall follow the same error format conventions already used by the service.
