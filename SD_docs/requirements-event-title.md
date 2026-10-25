# Requirements: Correcting an Event Title

## 1. Original Request

"I entered an incorrect event name. I want to correct the name displayed in the client's gallery."

## 2. Current State and Verified Evidence

### Fact 1: Event title field
The `EventRecord` type contains a `title` field of type `string`.

**Evidence:** `src/domain/types.ts` — `EventRecord`.

### Fact 2: Client gallery returns the event title
The `getClientGallery` method returns `title: event.title` when the event belongs to the client and the gallery is published.
###something i didnt do

**Evidence:** `src/services/event-service.ts` — `getClientGallery`.

### Fact 3: No title-update endpoint was found in the current API
The inspected API defines endpoints for listing events, viewing galleries, and submitting photo selections. No endpoint for updating an event title appears in the inspected file.

**Evidence:** `src/http/app.ts`.

### Fact 4: Existing tests do not test title updates
The five tests cover identity requirements, photographer event separation, photo selection, and client gallery access. They do not test changing an event title.

**Evidence:** `tests/event-api.test.ts`.

### Fact 5: The current store uses in-memory event data
The `MemoryStore` contains sample events, and its event records include a `title` field.

**Evidence:** `src/data/memory-store.ts`.

## 3. Actors

- **Photographer:** May need to correct the title of an event they own. This permission must be confirmed.
- **Client:** Views the published gallery and its event title.

## 4. Scope

The requested change concerns correcting an event title and displaying the corrected title in the client's gallery.

The exact editing interface and API design are not decided in this document.

## 5. Functional Requirements — Proposed

- **FR1:** An authorized user should be able to correct the title of an event they are permitted to manage.
- **FR2:** After a valid title change is saved, the client's gallery should display the updated title.
- **FR3:** A user must not be able to change an event belonging to another photographer without permission.

These are proposed requirements and need confirmation, especially the permitted actor and authorization rules.

## 6. Non-Functional Requirements — To Confirm

- **NFR1:** The update should not unintentionally change the event's photos, client association, or service options.
- **NFR2:** The system should provide a clear response when an update fails.

The expected error behavior and validation rules still need to be defined.

## 7. Questions and Uncertainties

### Actors and permissions
- Is the photographer the only person allowed to correct an event title?
- Can a client request a correction, or edit the title themselves?

### Functional behavior
- Should the change update only the title shown in the gallery, or also the title wherever the event is listed?
- Should the updated title appear immediately in an already-open gallery, or when the gallery is loaded again?

### Validation and ambiguity
- Can a title contain only spaces?
- Is an empty title allowed?
- Is there a maximum title length?
- Should leading and trailing spaces be removed?

### Constraints and assumptions
- Does the event title need to be unique?
- Must the event already be published for its title to be corrected?
- The current code uses an in-memory store. Is persistence after server restart required for this assignment?

### Missing information
- What should happen if the event ID does not exist?
- What should happen if the user is not authorized?
- What response should be returned when the new title is invalid?

## 8. Acceptance Criteria — Conditional

### AC1: Successful title correction
**GIVEN** an existing event and a user authorized to edit it  
**WHEN** the user submits a valid new title  
**THEN** the title is saved and the client's gallery returns the new title when loaded again.

### AC2: Unauthorized update
**GIVEN** an event that belongs to a different photographer  
**WHEN** a user without permission attempts to change its title  
**THEN** the system rejects the update and does not change the event title.

### AC3: Invalid title
**GIVEN** an existing event  
**WHEN** an authorized user submits a title that violates the agreed validation rules  
**THEN** the system rejects the update and preserves the previous title.

These criteria are conditional because the editing permissions and title-validation rules have not yet been confirmed.

## 9. AI Review and Decisions

### Decision 1: Do not assume that a title-update API already exists
**Decision:** Treat the update capability as missing from the inspected API until further evidence is found.

**Reason:** The inspected `src/http/app.ts` defines listing, gallery, and selection endpoints, but no title-update endpoint. This is based on the inspected file, not on an assumption about the entire repository.

### Decision 2: Do not assume that the client may edit the title
**Decision:** Keep the authorized editor as an open question; the photographer is a proposed actor, not a verified permission.

**Reason:** The current code supports client gallery viewing and photographer event access, but the inspected files do not define a title-editing permission.

## 10. Open Questions

The main unresolved decisions are who can edit a title, the validation rules, the expected error responses, and whether the updated title must be persisted beyond the current in-memory store.
