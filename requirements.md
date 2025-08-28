# Requirement Specifications

## 1. User Authentication

### Overview (User Authentication)

Enables users (Guest or Host) to register, log in, and maintain secure sessions. Uses JWT for stateless authentication.

### API Endpoints (User Authentication)

* `POST /api/v1/auth/register` → Register a new user
* `POST /api/v1/auth/login` → Authenticate and return JWT
* `GET /api/v1/auth/profile` → Get logged-in user profile
* `PUT /api/v1/auth/profile` → Update user profile

### Input/Output (User Authentication)

#### Register (POST /auth/register)

**Input (JSON):**

```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "StrongPass123",
  "role": "guest"
}
```

**Output (201 Created):**

```json
{
  "message": "User registered successfully",
  "userId": "uuid",
  "token": "jwt_token_here"
}
```

#### Login (POST /auth/login)

**Input (JSON):**

```json
{
  "email": "john@example.com",
  "password": "StrongPass123"
}
```

**Output (200 OK):**

```json
{
  "message": "Login successful",
  "token": "jwt_token_here"
}
```

### Validation Rules (User Authentication)

* Email must be unique and valid format.
* Password must be ≥ 8 chars, include uppercase, lowercase, number.
* Role must be one of: guest, host.

### Performance Criteria (User Authentication)

* Registration/login should complete in < 1 second under 200 concurrent requests.
* JWT verification must be stateless and not exceed 50ms per request.

## 2. Property Management

### Overview (Property Management)

Hosts can create, update, delete, and retrieve property listings. Guests can search/filter listings.

### API Endpoints (Property Management)

* `POST /api/v1/properties` → Create property (Host only)
* `GET /api/v1/properties/:id` → Get property details
* `PUT /api/v1/properties/:id` → Update property (Host only)
* `DELETE /api/v1/properties/:id` → Delete property (Host only)
* `GET /api/v1/properties` → Search/filter properties

### Input/Output (Property Management)

#### Create Property (POST /properties)

**Input (JSON):**

```json
{
  "title": "Beachfront Villa",
  "description": "Luxury villa with ocean view",
  "location": "Lagos, Nigeria",
  "price": 150,
  "amenities": ["WiFi", "Pool", "Parking"],
  "maxGuests": 4,
  "availability": {
    "startDate": "2025-09-01",
    "endDate": "2025-12-31"
  }
}
```

**Output (201 Created):**

```json
{
  "message": "Property created successfully",
  "propertyId": "uuid"
}
```

### Validation Rules (Property Management)

* Title: required, max 100 chars.
* Price: must be positive integer/float.
* Location: non-empty string.
* Amenities: must be valid predefined options.
* Dates: startDate ≤ endDate.

### Performance Criteria (Property Management)

* Search must return results in < 2 seconds for up to 10,000 listings.
* Filtering must support pagination (default 20 items per page).

## 3. Booking System

### Overview (Booking System)

Guests can book properties for specific dates, system prevents double-bookings, tracks statuses (pending, confirmed, canceled, completed).

### API Endpoints (Booking System)

* `POST /api/v1/bookings` → Create booking (Guest)
* `GET /api/v1/bookings/:id` → Get booking details
* `PUT /api/v1/bookings/:id/cancel` → Cancel booking
* `GET /api/v1/bookings` → List user bookings

### Input/Output (Booking System)

#### Create Booking (POST /bookings)

**Input (JSON):**

```json
{
  "propertyId": "uuid",
  "checkIn": "2025-09-10",
  "checkOut": "2025-09-15",
  "guests": 2
}
```

**Output (201 Created):**

```json
{
  "message": "Booking created successfully",
  "bookingId": "uuid",
  "status": "pending"
}
```

### Validation Rules (Booking System)

* Check-in must be ≥ today.
* Check-out must be > check-in.
* Guests ≤ property.maxGuests.
* No overlapping booking allowed for the same property and date range.

### Performance Criteria (Booking System)

* Availability check must complete in < 500ms.
* System must handle 1,000 concurrent booking requests without double-booking errors.
