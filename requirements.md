# Airbnb Clone Backend – Feature Specifications

This document details the technical and functional requirements for core backend features including User Authentication, Property Management, and the Booking System.

---

## 1. User Authentication

### ✅ Functional Requirements
- Users should register as Guest or Host.
- Users must log in using Email/Password or OAuth.
- JWT will be used for secure authentication sessions.

### 🔧 API Endpoints
- **POST /api/v1/auth/register**
  - **Input**: `{ "name": "John", "email": "john@example.com", "password": "secret" }`
  - **Validation**: Email must be unique, password ≥ 8 characters.
  - **Output**: `{ "token": "<jwt>", "user": { "id": 1, "role": "guest" } }`
- **POST /api/v1/auth/login**
  - **Input**: `{ "email": "john@example.com", "password": "secret" }`
  - **Output**: `{ "token": "<jwt>", "user": { "id": 1, "role": "guest" } }`
- **GET /api/v1/users/me**
  - **Auth Required**: Yes (Bearer Token)
  - **Output**: Current user’s profile.

### 📏 Performance & Security Criteria
- Token expiry: 24 hours.
- Rate limit: 5 login attempts/min per IP.
- All passwords must be hashed using bcrypt.

---

## 2. Property Management

### ✅ Functional Requirements
- Hosts can create, update, and delete property listings.
- Listings include title, description, location, price, photos, and amenities.

### 🔧 API Endpoints
- **POST /api/v1/properties/**
  - **Input**: `{ "title": "2BR Flat", "location": "Lagos", "price": 200, ... }`
  - **Validation**: Required fields: title, location, price. Price must be > 0.
  - **Auth Required**: Yes (Host role only)
- **GET /api/v1/properties?location=lagos&priceMax=300**
  - **Output**: Array of properties with filtering.
- **PATCH /api/v1/properties/:id**
  - **Input**: Partial fields like `{ "price": 250 }`
  - **Auth Required**: Yes (Host and owner only)
- **DELETE /api/v1/properties/:id**
  - **Auth Required**: Yes (Host and owner only)

### 📏 Performance & Storage
- Pagination limit: 10–50 per page.
- Images are stored via cloud (or locally, in dev setup).

---

## 3. Booking System

### ✅ Functional Requirements
- Guests can book available properties.
- Prevent overlapping bookings.
- Bookings have statuses: pending, confirmed, cancelled, completed.

### 🔧 API Endpoints
- **POST /api/v1/bookings/**
  - **Input**: `{ "property_id": 3, "start_date": "2025-05-20", "end_date": "2025-05-25" }`
  - **Validation**: Dates must not overlap with existing bookings.
  - **Auth Required**: Yes (Guest only)
  - **Output**: Booking object with status: pending
- **GET /api/v1/bookings/me**
  - **Output**: List of user’s bookings.
- **DELETE /api/v1/bookings/:id**
  - **Validation**: Can only cancel if status is pending or confirmed.

### 📏 Performance & Data Integrity
- Booking date checks must use database transactions to avoid race conditions.
- Index on `property_id + start_date + end_date` to speed up availability queries.

---

## 📌 Notes
- All responses use standard HTTP status codes.
- All endpoints must include structured error responses.
- Unit and integration tests are required for all listed endpoints.


