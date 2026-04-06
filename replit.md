# Workspace

## Overview

pnpm workspace monorepo using TypeScript. Contains a real-time multiplayer "Đuổi Hình Bắt Chữ" (Picture Guessing) game.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM (via `@workspace/db`)
- **Real-time**: Socket.IO (server: `artifacts/api-server`, client: `socket.io-client` in frontend)
- **Auth**: Session-based via `express-session` (stored server-side)
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle for API server)
- **Frontend**: React + Vite + Tailwind CSS v4 + shadcn/ui

## Architecture

### Game Flow
1. User logs in/registers via `/api/auth/login` (auto-detects new vs existing user by username)
2. Lobby: create room (generates 6-char random code, joins as Host) or join existing room by code
3. Game Room:
   - **Host**: Sets image URL + secret answer → broadcasts to all players
   - **Players**: See image, type guesses in chat
   - If guess matches secret answer (case-insensitive) → winner gets +10 points, score updated in DB

### Socket.IO Events
- Client emits: `join_room`, `chat_message`, `set_image`, `leave_room`
- Server emits: `room_joined`, `room_error`, `player_joined`, `player_left`, `new_message`, `image_update`, `score_update`, `host_changed`

## Artifacts

- **`artifacts/picture-guess`** — Frontend (React + Vite, served at `/`)
- **`artifacts/api-server`** — Backend (Express + Socket.IO, served at `/api` and `/socket.io`)

## Key Commands

- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- `pnpm --filter @workspace/api-server run dev` — run API server locally
- `pnpm --filter @workspace/picture-guess run dev` — run frontend locally

## Database Schema

### users
- `id` — serial primary key
- `username` — unique text
- `password` — plain text (per user's spec requirement)
- `total_score` — integer (default 0)
- `created_at` — timestamp

See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details.
