# Logistics Management System - Backend (https://github.com/Cruz1122/logistics-backend.git)

This system enables distribution companies to optimize their operations through microservices for managing users, orders, inventory, geolocation, and reports.

---

## Technologies

- **Core:** Node.js, Express.js
- **Databases:** PostgreSQL (for relational data) with Prisma ORM, and MongoDB (for geolocation data) with Mongoose.
- **Containerization:** Docker & Docker Compose.
- **API Gateway:** Custom implementation using `http-proxy-middleware`.
- **CI/CD:** GitHub Actions.
- **Authentication:** JWT (JSON Web Tokens) for securing endpoints.
- **Notifications:** Nodemailer (for emails) and Twilio (for SMS).
- **API Documentation:** Swagger (OpenAPI).
- **File Handling:** Multer for file uploads, with support for CSV and Excel.

---

## Backend Architecture

The backend is designed using a microservices architecture, where each service is responsible for a specific domain. An API Gateway acts as a single entry point for all client requests.

```plaintext
├── api-gateway/
├── auth-service/
├── inventory-service/
├── orders-service/
├── geo-service/
├── reports-service/
├── docker-compose.yml
└── .env (local)
```

Each microservice is a standalone app with its own `Dockerfile`, `package.json`, and business logic.

---

## Microservices

### 1. API Gateway

- **GitHub:** [api-gateway](https://github.com/Cruz1122/logistics-backend/tree/develop/api-gateway)
- **Description:** Single entry point for all incoming requests. Routes traffic to the appropriate microservice and serves a status page.
- **Base Path:** `/`
- **Features:**
    - Reverse proxy for all other microservices.
    - Serves a static status page (`/`).
- **Technologies:** `express`, `cors`, `http-proxy-middleware`.

### 2. Authentication Service (`auth-service`)

- **GitHub:** [auth-service](https://github.com/Cruz1122/logistics-backend/tree/develop/auth-service)
- **Description:** Manages user authentication, roles, and permissions.
- **Base Path:** `/auth`
- **Features:**
    - User registration (`/signup`)
    - Email verification and two-factor authentication (2FA) (`/verify-email`, `/verify-two-factor`).
    - User login (`/signin`)
    - Password management (reset, change).
    - CRUD operations for Users, Roles, and Permissions.
- **Technologies:** `express`, `@prisma/client`, `bcrypt`, `jsonwebtoken`, `nodemailer`, `twilio`, `swagger-jsdoc`.

### 3. Inventory Service (`inventory-service`)

- **GitHub:** [inventory-service](https://github.com/Cruz1122/logistics-backend/tree/develop/inventory-service)
- **Description:** Manages all inventory aspects, including products, categories, suppliers, and warehouses.
- **Base Path:** `/inventory`
- **Features:**
    - CRUD for Products, Categories, Suppliers, and Warehouses.
    - Manages stock levels and product movements between warehouses.
    - Bulk data import from CSV/Excel files for products and warehouses.
- **Technologies:** `express`, `@prisma/client`, `axios`, `csv-parser`, `xlsx`, `multer`, `nodemailer`.

### 4. Orders Service (`orders-service`)

- **GitHub:** [orders-service](https://github.com/Cruz1122/logistics-backend/tree/develop/orders-service)
- **Description:** Handles the full order lifecycle from creation to delivery assignment.
- **Base Path:** `/orders`
- **Features:**
    - CRUD operations for orders.
    - Delivery staff management.
    - Assigns available delivery personnel to new orders.
    - Address geocoding using the Google Maps API.
- **Technologies:** `express`, `@prisma/client`, `@googlemaps/google-maps-services-js`, `axios`, `nodemailer`.

### 5. Geolocation Service (`geo-service`)

- **GitHub:** [geo-service](https://github.com/Cruz1122/logistics-backend/tree/develop/geo-service)
- **Description:** Manages real-time geolocation tracking for deliveries.
- **Base Path:** `/geo`
- **Features:**
    - Saves and updates delivery staff locations.
    - Provides endpoints for location history and finding nearby couriers.
    - Real-time tracking via WebSockets.
- **Technologies:** `express`, `mongoose`, `socket.io`, `axios`.

### 6. Reports Service (`reports-service`)

- **GitHub:** [reports-service](https://github.com/Cruz1122/logistics-backend/tree/develop/reports-service)
- **Description:** Generates and serves reports based on data from other microservices.
- **Base Path:** `/reports`
- **Features:**
    - Generates delivery reports in PDF and XLSX formats.
    - Aggregates data from the `orders`, `inventory`, and `auth` services.
- **Technologies:** `express`, `pdfkit`, `exceljs`, `axios`, `moment`.

---

## How to Run the Project

### Prerequisites

- Docker
- Docker Compose

### Steps

1. **Clone the repository:**
    ```bash
    git clone https://github.com/Cruz1122/logistics-backend.git
    cd logistics-backend
    ```

2. **Set up environment variables:**
    Create a `.env` file in the project root by copying `.env.example` and completing the required values.

3. **Build and run the services:**
    ```bash
    docker-compose up --build
    ```
    This command builds the Docker images for each microservice and starts them. Services will be accessible through the API Gateway at `http://localhost:3000`.

---
<br><br>
# Logistics Management System - Frontend (https://github.com/Cruz1122/logistics-frontend.git)

This is the frontend for the Logistics Management System, a full web application built with React. It provides an intuitive user interface to interact with backend microservices, enabling smooth management of users, inventory, orders, and real-time delivery tracking.

---

## Technologies

- **Framework:** React 19 with Vite for a fast development experience.
- **State Management:** Redux Toolkit with `redux-persist` to retain state across sessions.
- **Routing:** React Router v7 for declarative and protected routing.
- **HTTP Client:** Axios for making requests to the backend API Gateway.
- **Maps and Geolocation:** Google Maps API, integrated via `@react-google-maps/api`, to display maps and draw routes.
- **Real-Time Communication:** Socket.IO Client to receive live location updates from the `geo-service`.
- **UI Components:** Combination of custom components and icons from `react-icons` and `antd`.
- **Notifications:** `react-toastify` for friendly, non-intrusive alerts.
- **Authentication:** Client-side handling of JWT (JSON Web Tokens) for secure session management.

---

## Features

### 1. Authentication

- **Login and Registration:** Secure flows for user sign-up and sign-in.
- **Two-Factor Authentication (2FA):** Support for receiving verification codes via Email or SMS.
- **Email Verification:** A flow to confirm the user's email after registration.
- **Password Management:**
    - Securely reset a forgotten password via email code.
    - Change the current password from the user profile.
- **Protected Routes:** Access to various parts of the app is restricted based on authentication status and assigned user permissions.

### 2. Main Dashboards

- **Home Page:** A welcome screen with tracking code search and quick access to login/register.
- **Dashboard:** A central panel where users access various management modules based on roles and permissions.
- **User Profile:** A dedicated page where users can view account info and log out.

### 3. Management Modules (CRUD)

The app includes full management panels for all main system entities, allowing authorized users to Create, Read, Update, and Delete records:
- User Management
- Roles and Permissions Management
- Categories, Products, and Suppliers
- States and Cities
- Warehouses and Inventory (Product-Warehouse stock)
- Orders and Delivery Personnel

### 4. Geolocation and Tracking

- **Real-Time Tracking:** A map view showing a courier’s live location, updated via WebSockets.
- **Global View Map:** An administrative view displaying all active deliveries, warehouses, and pending orders on one map.
- **Route Information:** The tracking map shows the estimated distance and travel time between courier and destination.

### 5. Reports

- **Download Reports:** Authorized users can download delivery reports in **PDF** and **Excel** formats directly from the management panel.

---

## How to Run the Frontend

### Prerequisites

- Docker
- Docker Compose

### Steps

1. **Clone the repository:**
    ```bash
    git clone https://github.com/Cruz1122/logistics-frontend.git
    cd logistics-frontend
    ```

2. **Configure Environment Variables:**
    Create a `.env` file at the project root by copying from `.env.example`. Fill in the necessary values, especially the backend API Gateway URL.

3. **Build and run the services:**
    ```bash
    docker-compose up --build
    ```
    This command builds the Docker image for the frontend and starts it. The app will be available at `http://localhost:5173`.
