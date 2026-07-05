# LearnCode

A LeetCode-style coding practice platform built with Next.js. Users can browse problems, write and submit solutions in an in-browser code editor, get their submissions judged automatically, track submission history, organize problems into playlists, and get help from an integrated AI chat assistant.

## Features

- **Authentication** — Sign up / sign in via Clerk, including guest access and role-based authorization (admin vs. regular user).
- **Problem management** — Create, edit, and browse coding problems (admin-only creation/editing).
- **Code judging** — Submit solutions and have them executed/judged against test cases via Judge0.
- **Submission history** — View past submissions and per-test-case results.
- **Playlists** — Group problems into custom playlists for focused practice.
- **AI Chat Assistant** — Get help from an AI chatbot powered by Google's Generative AI, streamed via the Vercel AI SDK.
- **Modern UI** — Built with Tailwind CSS, Radix UI / shadcn components, and Monaco Editor for in-browser code editing.

## Tech Stack

- **Framework:** [Next.js 16](https://nextjs.org/) (App Router, React 19)
- **Database:** PostgreSQL + [Prisma ORM](https://www.prisma.io/)
- **Auth:** [Clerk](https://clerk.com/)
- **Code execution:** [Judge0](https://judge0.com/) API
- **AI:** Google Generative AI via the [Vercel AI SDK](https://sdk.vercel.ai/)
- **UI:** Tailwind CSS, Radix UI, shadcn/ui, Monaco Editor
- **Containerization:** Docker (PostgreSQL runs in a container)

## Prerequisites

Before you begin, make sure you have installed:

- [Node.js](https://nodejs.org/) v20 or later
- [Docker](https://www.docker.com/) (for running PostgreSQL locally)
- npm (comes with Node.js)

## Getting Started

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd learnCode
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Create a `.env` file in the project root with the following variables:

```bash
# Database (matches docker-compose.yml defaults — update if you change them there)
DATABASE_URL="postgresql://postgres:postgres123@localhost:5432/learnCode?schema=public"

# Clerk authentication — get these from https://dashboard.clerk.com
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
CLERK_SECRET_KEY=your_clerk_secret_key

# Judge0 (code execution/judging) — e.g. via RapidAPI
JUDGE0_API_URL=your_judge0_api_url
JUDGE0_API_KEY=your_judge0_api_key
RAPIDAPI_HOST=your_rapidapi_host

# Google Generative AI (for the AI chat assistant)
GOOGLE_GENERATIVE_AI_API_KEY=your_google_ai_api_key
```

### 4. Start the database

The PostgreSQL database runs in Docker. Start it with:

```bash
docker compose up -d
```

This starts a Postgres 17 container (`learncode_build`) on port `5432`, backed by a persistent Docker volume.

> Note: `npm run dev` already runs `docker compose up -d` automatically before starting the dev server, so this step is optional if you use `npm run dev`.

### 5. Set up the database schema

Apply Prisma migrations and generate the Prisma Client:

```bash
npx prisma migrate dev
npx prisma generate
```

### 6. Run the development server

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser to see the app.

## Available Scripts

| Command         | Description                                              |
| --------------- | ---------------------------------------------------------|
| `npm run dev`   | Starts the Postgres container and the Next.js dev server |
| `npm run build` | Builds the app for production                            |
| `npm run start` | Runs the production build                                 |
| `npm run lint`  | Runs ESLint                                                |

## Project Structure

```
app/                # Next.js App Router pages & API routes
  (auth)/           # Auth-related pages (sign in / sign up)
  (root)/           # Main application pages
  api/              # API route handlers (including AI chat)
  create-problem/   # Admin: create a new problem
  edit-problem/     # Admin: edit an existing problem
  problem/          # Problem detail / solving page

modules/            # Feature modules (auth, home, problems, profile)
components/         # Shared/reusable UI components
hooks/              # Custom React hooks
lib/                # Utilities (db client, Judge0 integration, etc.)
prisma/             # Prisma schema & migrations
```

## Database Schema (overview)

The core Prisma models are:

- `User` — application users (with roles)
- `Problem` — coding problems
- `Submission` — user submissions for a problem
- `TestCaseResult` — per-test-case results for a submission
- `ProblemSolved` — tracks which problems a user has solved
- `Playlist` / `ProblemInPlaylist` — user-created problem playlists

## Deployment

This app can be deployed on any platform that supports Next.js (e.g. [Vercel](https://vercel.com/)). Make sure to:

1. Provision a PostgreSQL database and set `DATABASE_URL` accordingly.
2. Set all the environment variables listed above in your hosting provider's dashboard.
3. Run `npx prisma migrate deploy` against your production database as part of your deployment pipeline.
