This fork implements a “recurring booking” feature. 


Also, to the extent time permitted, it improves architecture by enforcing previously-disregarded SOLID principals, improving “DRY”s and enabling easier feature additions and scaling.


## New Feature Implemented: "Recurring Booking" — users may now:


1. Create a recurring booking for a desk.
2. See recurring bookings reflected in their calendar.
3. Be protected from making new recurring bookings which conflict with existing ones.
4. Be protected from entering erroneous dates (via date range input validation).


## Mitigation of Architiceture Flaws


Additional improvements were made to security, scalability and performance through architectural refactoring. The previous implementation had three core problems:

1. **No abstraction layer**: React components were tightly coupled to HTTP implementation (TanStack Query), directly handling API calls, errors, headers, and status codes.

2. **Poor separation of concerns**: UI components accessed low-level API details instead of simply requesting data and handling results.

3. **Missing contracts**: No clear interfaces defined available operations, making the codebase difficult to extend and maintain.


## Architectural Solutions

The refactor introduced a unified API client layer (`SeatlyApiClient`) that addresses all three problems:

**Key improvements:**
- Components now call simple, typed methods (`createBooking()`, `getDesks()`) instead of managing HTTP requests
- All HTTP logic centralized in one place, making it easy to swap protocols (GraphQL, WebSockets) or frameworks
- Clear TypeScript interfaces define contracts between layers (see `frontend/src/types/`)
- Security improved by encapsulating sensitive implementation details (headers, error parsing, status codes)

**Additional DRY improvements:**
- Created reusable `Button` and `Input` components to eliminate repetitive JSX across pages


## Backend-specific additions and improvements


1. New Recurring Booking Endpoint


POST /desks/{deskId}/bookings/recurring
Accepts start/end times + recurrence end date
Returns list of all created bookings


2. Business Logic (DeskManager.kt)


createRecurringBooking() method with @Transactional
calculateRecurringDates() helper function
Atomic validation (checks ALL dates before creating ANY bookings)


3. New Types/DTOs


CreateRecurringBookingRequest (controller layer)
CreateRecurringBookingCommand (business layer)


4. Tests (DeskControllerTest.kt)


Three new test cases validating recurring booking behavior


