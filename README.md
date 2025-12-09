# Movie Watchlist API

A backend API for managing a personalized movie watchlist. Built with Node.js, Express, and PostgreSQL, this application allows users to register, browse seeded movies, and manage their own watchlist with tracking statuses (Planned, Watching, Completed, Dropped), ratings, and notes.

## Features

-   **User Authentication**: Secure registration and login using JWT (JSON Web Tokens).
-   **Movie Management**: Browse a pre-seeded database of popular movies.
-   **Watchlist**:
    -   Add movies to your personal watchlist.
    -   Update status (Planned, Watching, Completed, Dropped).
    -   Add personal ratings and notes.
    -   Remove movies from the watchlist.
-   **Data Validation**: Robust input validation using Zod.
-   **Security**: Password hashing with Bcryptjs.

## Tech Stack

-   **Runtime**: [Node.js](https://nodejs.org/)
-   **Framework**: [Express.js](https://expressjs.com/)
-   **Database**: [PostgreSQL](https://www.postgresql.org/)
-   **ORM**: [Prisma](https://www.prisma.io/)
-   **Authentication**: [check JWT](https://jwt.io/) & [Bcryptjs](https://www.npmjs.com/package/bcryptjs)
-   **Validation**: [Zod](https://zod.dev/)

## Prerequisites

Before running this project, ensure you have the following installed:

-   [Node.js](https://nodejs.org/) (v14 or higher)
-   [npm](https://www.npmjs.com/) (Node Package Manager)
-   [PostgreSQL](https://www.postgresql.org/) (Local instance or cloud provider like Neon/Supabase)

## Getting Started

Follow these steps to set up the project locally.

### 1. Clone the Repository

```bash
git clone <repository-url>
cd movie-watchlist-api
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure Environment Variables

Create a `.env` file in the root directory and add the following variables:

```env
# Database connection string
DATABASE_URL="postgresql://USER:PASSWORD@HOST:PORT/DATABASE?schema=public"

# JWT Secret for authentication
JWT_SECRET="your_super_secret_key"

# Port (Optional, defaults to 5001)
PORT=5001
```

### 4. Setup Database

Run the Prisma migrations to create the database tables:

```bash
npx prisma migrate dev --name init
```

Seed the database with initial movie data:

```bash
npm run seed:movies
```

### 5. Run the Server

Start the development server:

```bash
npm run dev
```

The server should now be running at `http://localhost:5001`.

## API Endpoints

### Authentication

| Method | Endpoint | Description | Body Parameters |
| :--- | :--- | :--- | :--- |
| `POST` | `/auth/register` | Register a new user | `email`, `password`, `name` |
| `POST` | `/auth/login` | Login user | `email`, `password` |
| `POST` | `/auth/logout` | Logout user | N/A |

### Watchlist

**Note**: All Watchlist endpoints require a valid JWT token in the `Authorization` header (`Bearer <token>`).

| Method | Endpoint | Description | Body Parameters |
| :--- | :--- | :--- | :--- |
| `POST` | `/watchlist` | Add to watchlist | `movieId` (UUID), `status` (Optional), `rating` (Int), `notes` (String) |
| `PUT` | `/watchlist/:id` | Update item | `status`, `rating`, `notes` |
| `DELETE` | `/watchlist/:id` | Remove item | N/A |

### Movies

Movies are currently populated directly into the database via the seed script.
The seeded user ID is `a9baa5b0-a02a-4f9f-b857-d1eab1bd4345`.

## Project Structure

```
src/
├── config/         # Database configuration
├── controllers/    # Request handlers (Auth, Watchlist)
├── middlewares/    # Auth and Validation middlewares
├── routes/         # API Route definitions
├── utils/          # Utility functions
├── validators/     # Zod schemas
└── server.js       # Entry point
prisma/
├── schema.prisma   # Database schema
└── seed.js         # Database seeding script
```

## License

This project is licensed under the ISC License.
