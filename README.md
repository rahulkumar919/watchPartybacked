# WatchParty Backend

Express + Socket.IO backend for YouTube Watch Party System.

## Features

- **Real-time synchronization** using Socket.IO
- **Role-based access control** (Host, Moderator, Participant)
- **Playback requests** for participants
- **Room chat** with persistence
- **PostgreSQL database** with Prisma ORM
- **Session-based authentication** with JWT

## Tech Stack

- **Runtime**: Node.js 20+
- **Framework**: Express.js
- **Real-time**: Socket.IO
- **Database**: PostgreSQL (Neon)
- **ORM**: Prisma
- **Validation**: Zod
- **Testing**: Vitest

## Local Development

### Prerequisites

- Node.js >= 20.0.0
- npm >= 9.0.0
- PostgreSQL database (local or Neon)

### Setup

1. **Clone the repository**
```bash
git clone https://github.com/rahulkumar919/watchPartybacked.git
cd watchPartybacked
```

2. **Install dependencies**
```bash
npm install
```

3. **Set up environment variables**
```bash
cd apps/server
cp .env.example .env
# Edit .env with your database credentials
```

4. **Run database migrations**
```bash
npm run db:migrate --workspace=apps/server
```

5. **Start development server**
```bash
npm run dev:server
```

Server will run on `http://localhost:4000`

## Environment Variables

Create `apps/server/.env`:

```env
# Database (get from Neon or local PostgreSQL)
DATABASE_URL="postgresql://user:password@host:5432/database?sslmode=require"
DIRECT_URL="postgresql://user:password@host:5432/database?sslmode=require"

# JWT Secret (generate with: openssl rand -base64 32)
JWT_SECRET="your-super-secret-jwt-key-here"
JWT_EXPIRES_IN="24h"

# Server
PORT=4000
NODE_ENV="development"

# CORS (comma-separated origins or * for all)
CORS_ORIGIN="http://localhost:3000"
```

## Deployment on Render

### Quick Deploy

1. **Create PostgreSQL Database on Render**
   - Go to Render Dashboard → New → PostgreSQL
   - Copy `Internal Database URL` and `External Database URL`

2. **Deploy Backend**
   - New → Web Service
   - Connect this GitHub repository
   - **Build Command**: `npm ci && npm run db:generate --workspace=apps/server && npm run build && npm run db:migrate:deploy --workspace=apps/server`
   - **Start Command**: `npm run start --workspace=apps/server`
   - Add environment variables (see below)

3. **Environment Variables on Render**
   - `NODE_ENV` = `production`
   - `PORT` = `10000`
   - `DATABASE_URL` = [Internal Database URL from Render]
   - `DIRECT_URL` = [External Database URL from Render]
   - `JWT_SECRET` = [Generate with `openssl rand -base64 32`]
   - `JWT_EXPIRES_IN` = `24h`
   - `CORS_ORIGIN` = `*` (or your frontend URL)

### Using render.yaml

This repository includes a `render.yaml` for automatic deployment:

```bash
# In Render Dashboard
New → Blueprint → Connect repository → Apply
```

## API Endpoints

### Health Check
```
GET /health
```

### Room Management
```
POST /api/rooms - Create a new room
POST /api/rooms/join - Join existing room
```

### Socket.IO Events

**Client → Server:**
- `join_room` - Join a room
- `play` - Start playback
- `pause` - Pause playback
- `seek` - Seek to position
- `change_video` - Change video
- `request_playback_action` - Request playback control
- `resolve_playback_request` - Approve/reject request
- `send_message` - Send chat message
- `assign_role` - Change user role (Host only)
- `remove_participant` - Remove/ban user (Host only)
- `transfer_host` - Transfer host role

**Server → Client:**
- `sync_state` - Full state sync
- `playback_updated` - Playback state changed
- `user_joined` - User joined room
- `user_left` - User left room
- `message_received` - New chat message
- `playback_request_created` - New playback request
- `playback_request_resolved` - Request approved/rejected
- `role_assigned` - Role changed
- `participant_removed` - User removed/banned
- `host_transferred` - Host role transferred

## Project Structure

```
watchPartybacked/
├── apps/
│   └── server/              # Backend application
│       ├── prisma/          # Database schema & migrations
│       ├── src/
│       │   ├── config.ts    # Configuration
│       │   ├── db/          # Database connection
│       │   ├── routes/      # REST API routes
│       │   ├── services/    # Business logic
│       │   ├── socket/      # Socket.IO handlers
│       │   ├── state/       # In-memory state management
│       │   ├── types/       # TypeScript types
│       │   └── utils/       # Utilities (RBAC, validation)
│       └── tests/           # Unit & integration tests
└── packages/
    └── types/               # Shared TypeScript types & Zod schemas
```

## Scripts

```bash
# Development
npm run dev:server          # Start dev server with hot reload

# Build
npm run build               # Build all workspaces (types → server)
npm run build:server        # Build server only

# Database
npm run db:generate --workspace=apps/server    # Generate Prisma Client
npm run db:migrate --workspace=apps/server     # Run migrations (dev)
npm run db:migrate:deploy --workspace=apps/server  # Run migrations (prod)
npm run db:studio --workspace=apps/server      # Open Prisma Studio

# Testing
npm run test:server         # Run unit tests
npm run test:integration    # Run integration tests
npm run typecheck           # TypeScript type check
```

## Testing

```bash
# Run all tests
npm run test:server

# Run integration tests
npm run test:integration

# Run with coverage
npm run test:coverage --workspace=apps/server
```

## License

MIT

## Support

For issues and questions, please open an issue on GitHub.
