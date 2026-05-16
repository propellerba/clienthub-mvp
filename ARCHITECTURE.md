# ClientHub Architecture Guide

## 1. Architecture Overview
ClientHub is a full-stack web application built on a decoupled, client-server architecture. It utilizes a **Modular Layered Architecture** on the backend to ensure scalability and a **Feature-Based Layout** on the frontend for maintainability.

- **Frontend:** A Single Page Application (SPA) utilizing React 18 and TypeScript, managed by Vite.
- **Backend:** A Node.js and Express API enforcing a strict separation of concerns (Routes -> Controllers -> Services -> Prisma).
- **Persistence:** A PostgreSQL relational database managed via Prisma ORM for type-safe data access.
- **Security:** Stateless authentication using JWT and middleware-based authorization.

---

## 2. Tech Stack Details
- **Frontend:** React 18, TypeScript, Vite, TanStack Query v5 (Server State), Zustand (Client State), Tailwind CSS, React Router v6.
- **Backend:** Node.js, Express, TypeScript, Prisma ORM, JWT (jsonwebtoken), Bcrypt.js, Zod (Validation).
- **Database:** PostgreSQL 15+.
- **Testing:** Vitest & React Testing Library (Frontend), Jest & Supertest (Backend).
- **Package Manager:** pnpm.

---

## 3. Folder Structure

### 3.1 Backend Structure (src/)
```text
├── config/         # Environment variables & Prisma client initialization
├── controllers/    # Request handling and response orchestration
├── middleware/     # Auth (JWT), validation (Zod), and error-handling filters
├── routes/         # Express route definitions grouped by resource
├── services/       # Pure business logic and database orchestration (Prisma)
├── utils/          # Helpers (Formatting, custom AppError class)
├── app.ts          # Express application setup
└── server.ts       # Server entry point
```

### 3.2 Frontend Structure (src/)
```text
├── components/     # Shared UI components (Button, Input, Modal)
├── config/         # API endpoints and env var validation
├── features/       # Feature-specific modules (auth, clients, projects)
│   ├── [feature]/
│   │   ├── components/ # Feature-specific components
│   │   ├── hooks/      # TanStack Query hooks
│   │   ├── services/   # Axios API calls
│   │   ├── types/      # Feature-specific TS interfaces
│   │   └── index.ts    # Public feature API
├── hooks/          # Shared custom hooks (useLocalStorage, etc.)
├── lib/            # API client setup (Axios interceptors)
├── store/          # Global Zustand stores
└── App.tsx         # Routing and Provider setup
```

---

## 4. Design Patterns

### 4.1 Backend: Service Layer Pattern
Controllers handle the HTTP request/response. The **Service Layer** contains the core business logic and interacts with the Prisma client. This ensures logic is reusable across different controllers or CLI tools and simplifies unit testing.

### 4.2 Frontend: Server State vs. Client State
- **Server State:** Handled exclusively by **TanStack Query**. All caching, revalidation, and loading states for API data are managed here.
- **Client State:** Handled by **Zustand** for global UI state (e.g., sidebar toggles, theme) or local `useState` for component-specific logic.

### 4.3 Database: Relational Integrity
- Follow **3NF (Third Normal Form)** for normalization.
- Use PostgreSQL foreign keys to link Contacts to Clients and Notes to Projects.
- All schema changes must be versioned through **Prisma Migrate**.

---

## 5. Coding Standards

### 5.1 General TypeScript Rules
- **Explicit Types:** Avoid `any`. Use `unknown` with type guards if the type is truly uncertain.
- **Interfaces:** Prefer `interface` for object definitions and `type` for unions/intersections.

### 5.2 Naming Conventions
- **Frontend Components:** PascalCase (e.g., `ClientList.tsx`).
- **Hooks:** camelCase with `use` prefix (e.g., `useAuth.ts`).
- **Database Tables:** snake_case and **plural** (e.g., `audit_logs`, `projects`).
- **Database Columns:** snake_case (e.g., `created_at`, `is_active`).
- **Boolean Fields:** Must start with `is_`, `has_`, or `can_`.

### 5.3 Error Handling
- **Backend:** Centralized error middleware. Use a custom `AppError` class extending `Error` to include `statusCode`.
- **Frontend:** Use Axios interceptors to catch 401 errors for token expiration and redirect to login.

---

## 6. API Design

### 6.1 RESTful Conventions
- **Versioning:** All routes prefixed with `/api/v1`.
- **Resource Naming:** Use plural nouns (e.g., `GET /api/v1/clients`).
- **Standard HTTP Codes:**
    - `200/201`: Success
    - `400`: Validation Error (Zod)
    - `401`: Missing/Expired JWT
    - `403`: Forbidden (Ownership/Role issues)
    - `404`: Resource not found

### 6.2 Authentication Strategy
- **Stateless JWT:** Tokens are issued upon login and sent in the `Authorization: Bearer <token>` header.
- **Validation:** Every protected route uses an `authMiddleware` to verify the JWT and attach the `userId` to the `req` object.

---

## 7. Testing Strategy

### 7.1 Backend Testing
- **Unit Tests:** Focus on Service layer logic using Jest, mocking the Prisma client.
- **Integration Tests:** Use Supertest to verify API endpoints from the route through the controller to the database.

### 7.2 Frontend Testing
- **Component Tests:** Use Vitest and React Testing Library to verify UI behavior (e.g., "calls `onSubmit` when button is clicked").
- **Hooks Testing:** Test complex custom hooks in isolation.

### 7.3 Database Isolation
- Use a dedicated PostgreSQL test database or separate schema to ensure test runs do not pollute development data. Prisma `$transaction` can be used to roll back changes after integration tests.