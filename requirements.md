# Backend Feature Requirement Specifications

## Project: ALX Airbnb Clone

This document defines the technical and functional requirements for key backend features of the Airbnb clone application.

---

## 1. User Authentication

### Description:

Handles secure user signup, login, and session management.

### API Endpoints:

- **POST /api/auth/signup**

  - **Input**: `{ "name": String, "email": String, "password": String }`
  - **Output**: `{ "message": "User registered successfully", "userId": String }`
  - **Validation**:
    - Email must be in a valid format.
    - Password must be at least 8 characters and hashed using bcrypt.
  - **Status Codes**:
    - `201 Created` on success
    - `400 Bad Request` for validation errors
    - `409 Conflict` if user already exists

- **POST /api/auth/login**

  - **Input**: `{ "email": String, "password": String }`
  - **Output**: `{ "token": JWT, "user": { "id": String, "name": String } }`
  - **Validation**:
    - Credentials must match existing user
  - **Status Codes**:
    - `200 OK` on success
    - `401 Unauthorized` for invalid credentials

- **GET /api/auth/profile**
  - **Headers**: `Authorization: Bearer <JWT>`
  - **Output**: User profile object
  - **Status Codes**:
    - `200 OK`
    - `403 Forbidden` if token is invalid

### Performance Criteria:

- Login response time ≤ 300ms
- Token expiry: 1 hour, with refresh support (optional)

---

## 2. Property Management

### Description:

Hosts can create, update, view, and delete property listings.

### API Endpoints:

- **POST /api/properties**

  - **Input**: `{ "title": String, "location": String, "price": Number, "description": String, "images": [String], "amenities": [String], "availability": [{ "from": Date, "to": Date }] }`
  - **Headers**: `Authorization: Bearer <JWT>`
  - **Output**: `{ "message": "Property created", "propertyId": String }`
  - **Validation**:
    - Required fields: title, location, price
    - Auth token must belong to a host
  - **Status Codes**:
    - `201 Created`
    - `403 Forbidden`
    - `400 Bad Request` for validation failure

- **GET /api/properties**

  - **Query Params**: `location, priceRange, dateRange`
  - **Output**: List of matching properties

- **PATCH /api/properties/:id**

  - **Input**: Any updatable field (title, price, etc.)
  - **Headers**: `Authorization: Bearer <JWT>`
  - **Output**: Updated property object

- **DELETE /api/properties/:id**
  - **Headers**: `Authorization: Bearer <JWT>`
  - **Output**: `{ "message": "Property deleted" }`

### Performance Criteria:

- Average listing fetch time ≤ 400ms
- Property search must support filtering (e.g., by price or date range)

---

## 3. Booking System

### Description:

Manages reservations, ensures availability, and tracks booking history.

### API Endpoints:

- **POST /api/bookings**

  - **Input**: `{ "propertyId": String, "checkIn": Date, "checkOut": Date, "guests": Number }`
  - **Headers**: `Authorization: Bearer <JWT>`
  - **Output**: `{ "message": "Booking confirmed", "bookingId": String }`
  - **Validation**:
    - Property must be available for the given dates.
    - Check-out must be after check-in.
    - No overlapping bookings allowed.
  - **Status Codes**:
    - `201 Created`
    - `400 Bad Request` for invalid dates
    - `409 Conflict` for overlapping bookings

- **GET /api/bookings/:id**

  - **Output**: Booking details
  - **Headers**: `Authorization: Bearer <JWT>`

- **DELETE /api/bookings/:id**
  - **Description**: Cancels the booking if within allowed timeframe

### Performance Criteria:

- Booking confirmation time ≤ 500ms
- Prevent race conditions with proper locking/transaction logic

---

## Additional Notes:

- All endpoints are RESTful and return JSON.
- Rate limiting and logging should be implemented.
- Input sanitation and secure headers should be enforced using middleware.

---
