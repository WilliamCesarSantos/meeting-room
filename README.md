# Meeting Room

Meeting Room is a backend API for room management and booking operations. The project exposes REST endpoints for authentication, room CRUD, and reservation CRUD, and includes an observability stack for metrics and tracing.

## What this project includes

- Authentication endpoint (`/auth/login`) with JWT-based security.
- Room management endpoints (`/rooms`) with pagination and filtering.
- Reservation endpoints (`/reservations`) with date and room filters.
- Local infrastructure for PostgreSQL, LocalStack (AWS SSM), Prometheus, Grafana, and Jaeger.
- Production-like Docker Compose profile with NGINX in front of the API service.

## Technology stack

### Application

- Java 25
- Quarkus 3.34.x
- Maven Wrapper (`./mvnw`)
- Quarkus REST + Jackson
- Hibernate ORM with Panache
- PostgreSQL JDBC driver
- Hibernate Validator
- SmallRye JWT (authentication/authorization)
- Micrometer + Prometheus registry
- SmallRye Health
- Quarkus REST Client + Fault Tolerance
- OpenTelemetry (tracing)
- Quarkus Amazon SSM client
- Caffeine cache

### Infrastructure (Docker Compose)

- PostgreSQL 16
- LocalStack (SSM)
- Prometheus
- Grafana
- Jaeger
- NGINX

## Main API routes

- `POST /auth/login`
- `GET/POST/PUT/DELETE /rooms`
- `GET/POST/PUT/DELETE /reservations`

## API functionality by group

### Auth API (`/auth`)

- `POST /auth/login`: authenticates a user and returns a JWT token used to call protected endpoints.

### Rooms API (`/rooms`)

- `GET /rooms`: lists rooms with pagination and optional filters such as `name` and `minCapacity`.
- `GET /rooms/{id}`: returns room details by id.
- `POST /rooms`: creates a new room.
- `PUT /rooms/{id}`: updates an existing room.
- `DELETE /rooms/{id}`: removes a room.
- Access profile: read operations are public; write operations require `ADMIN` role.

### Reservations API (`/reservations`)

- `GET /reservations`: lists reservations with pagination and optional filters such as `roomId` and `date`.
- `GET /reservations/{id}`: returns reservation details by id.
- `POST /reservations`: creates a reservation for a room and time window.
- `PUT /reservations/{id}`: updates an existing reservation.
- `DELETE /reservations/{id}`: deletes a reservation.
- Access profile: read operations are public; write operations require `USER` or `ADMIN` role.

## Run with Docker Compose (build included)

This project has a `prd` compose profile for the API (`meeting-room`) and NGINX (`nginx`).

### Prerequisites

- Docker Engine or Docker Desktop
- Docker Compose v2

### Step by step

1. Open a terminal in the project root.
2. Build the `meeting-room` image from `src/main/docker/Dockerfile.native`.
3. Start infrastructure and app services using the `prd` profile.
4. Check service status and access URLs.

```bash
docker compose --profile prd build
docker compose --profile prd up -d
docker compose --profile prd ps
```

### Access points

- API via NGINX: `http://localhost:8080`
- NGINX secondary port: `http://localhost:8081`
- Prometheus: `http://localhost:9090`
- Grafana: `http://localhost:3000` (default: `admin` / `admin`)
- Jaeger UI: `http://localhost:16686`

### Useful commands

```bash
docker compose --profile prd logs -f
docker compose --profile prd down
docker compose --profile prd down -v
```

## Optional: run in Quarkus dev mode (without API container)

If you only want supporting services in containers and run Quarkus from your machine:

```bash
docker compose up -d postgres prometheus grafana jaeger localstack
./mvnw quarkus:dev
```

In this mode, the app is usually available at `http://localhost:8080`.

