# Free Hosting Guide for Movie Watchlist API

This guide details how to host your Node.js + Prisma + PostgreSQL application for free using **Render** (Backend) and **Neon** (Database).

## Prerequisites

1.  **GitHub Account**: Your project code must be pushed to a GitHub repository.
2.  **Render Account**: Sign up at [render.com](https://render.com/).
3.  **Neon Account**: Sign up at [neon.tech](https://neon.tech/).

---

## Part 1: Database Setup (Neon)

Neon offers a generous free tier for PostgreSQL.

1.  **Create a Project**:
    -   Log in to Neon console.
    -   Click **"New Project"**.
    -   Name it (e.g., `movie-watchlist`).
    -   Select a region close to your user base.
    -   Click **"Create Project"**.

2.  **Get Connection String**:
    -   On the Dashboard, find the **Connection Details** section.
    -   Copy the **Connection String** (it looks like `postgres://user:password@endpoint.neon.tech/dbname...`).
    -   **Important**: Neon connection strings often end with `?sslmode=require`. For Prisma, we usually don't need to change much, but keep this string safe. You will need it mainly for the `DATABASE_URL` env variable.

---

## Part 2: Backend Deployment (Render)

Render is a cloud platform that makes it easy to deploy web services.

1.  **Create a Web Service**:
    -   Log in to Render Dashboard.
    -   Click **"New +"** -> **"Web Service"**.
    -   Connect your GitHub repository (`movie-watchlist-api`).

2.  **Configure Service**:
    -   **Name**: `movie-watchlist-api` (or any unique name).
    -   **Region**: Choose a region (ideally same as your Database).
    -   **Branch**: `main`.
    -   **Runtime**: `Node`.
    -   **Build Command**: `npm install && npx prisma generate`
        -   *Explanation*: Installs dependencies and generates the Prisma Client.
    -   **Start Command**: `npm start`
        -   *Note*: Ensure your `package.json` has `"start": "node src/server.js"`.
    -   **Instance Type**: **Free**.

3.  **Environment Variables**:
    -   Scroll down to the **"Environment Variables"** section and click **"Add Environment Variable"**.
    -   Add the following:
        -   **Key**: `DATABASE_URL`
        -   **Value**: Paste the connection string from Neon. **Crucial**: Adding `?connect_timeout=300` at the end of the URL is sometimes helpful for serverless DBs, but standard Neon string usually works. To be safe for Prisma migrations, ensure the URL ends with `?sslmode=require`.
        -   **Key**: `JWT_SECRET`
        -   **Value**: Generate a strong random string (e.g., use a password generator).

4.  **Deploy**:
    -   Click **"Create Web Service"**.
    -   Render will start building your app. Watch the logs. It will take a few minutes.

---

## Part 3: Running Migrations & Seeding

Since the database is new, it has no tables. You need to run migrations.

### Option A: Connect and Run Locally (Easiest)
You can run migrations from your local machine against the *remote* database.

1.  In your **local** project folder, create a temporary `.env.production` file or just modify your `.env` *temporarily*.
2.  Replace `DATABASE_URL` in your local `.env` with the **Neon Connection String**.
3.  Run the migration command:
    ```bash
    npx prisma migrate deploy
    ```
    *Note: `migrate deploy` is for production. It applies pending migrations without resetting the DB.*
4.  Run the seed command:
    ```bash
    npm run seed:movies
    ```
5.  **Revert** your local `.env` file back to your local database URL.

### Option B: Run via Render Build Command
Alternatively, you can modify the **Build Command** in Render settings:
`npm install && npx prisma generate && npx prisma migrate deploy`
*However, be careful with this as it runs on every build. Seeding typically shouldn't run on every build.*

---

## Part 4: Testing

1.  Once Render says **"Live"**, copy the URL (e.g., `https://movie-watchlist-api.onrender.com`).
2.  Test the endpoints using Postman or Curl:
    -   **Register**: `POST /auth/register`
    -   **Login**: `POST /auth/login`
    -   **Get Watchlist**: `GET /watchlist` (don't forget the Bearer Token).

## Troubleshooting

-   **"P1001: Can't reach database"**: Check if `DATABASE_URL` is correct. Ensure "SSL" is enabled in the query string if required by the provider.
-   **Build Fails**: Check the logs on Render. Often related to missing dependencies or syntax errors.
-   **Cold Starts**: On the Free Tier, Render spins down your service after inactivity. The first request might take 50+ seconds. This is normal for free hosting.
