# Chroma - Virtual Pet Game

## Overview
Chroma is a Tamagotchi-style virtual pet game featuring a mystical cat named Chroma. Users must log in daily to feed Chroma and maintain her health. Neglect leads to visual consequences, and if left unfed for 3 days, Chroma will die and require resurrection through a secret phrase.

## Tech Stack
- **Frontend**: React, TypeScript, Vite, TailwindCSS, shadcn/ui
- **Backend**: Express, Node.js
- **Database**: PostgreSQL (Neon) with Drizzle ORM
- **Authentication**: Replit Auth (OIDC)
- **Routing**: Wouter
- **State Management**: TanStack Query

## Database Schema

### Users Table
- `id` (varchar): Primary key, auto-generated UUID
- `email` (varchar): User email (unique)
- `firstName` (varchar): User's first name
- `lastName` (varchar): User's last name
- `profileImageUrl` (varchar): Profile picture URL
- `createdAt` (timestamp): Account creation time
- `updatedAt` (timestamp): Last update time

### Pets Table
- `id` (varchar): Primary key, auto-generated UUID
- `userId` (varchar): Foreign key to users table
- `name` (text): Pet name (default: "Chroma")
- `state` (text): Current state (healthy, hungry, critical, dead)
- `lastFed` (timestamp): Last feeding time
- `streak` (integer): Daily feeding streak count
- `resurrectionCount` (integer): Number of times resurrected
- `createdAt` (timestamp): Pet creation time

### Emotional Logs Table
- `id` (varchar): Primary key, auto-generated UUID
- `petId` (varchar): Foreign key to pets table
- `message` (text): Emotional message/log entry
- `tone` (text): Message tone (content, hungry, critical, dead, resurrected)
- `createdAt` (timestamp): Log entry time

### Sessions Table
- `sid` (varchar): Session ID (primary key)
- `sess` (jsonb): Session data
- `expire` (timestamp): Session expiration time

## Game Mechanics

### Daily Feeding System
- Users can feed Chroma once per 24 hours
- Feeding resets at midnight local time
- Each successful feed increases the daily streak
- Emotional log generated after each feed

### Pet State System
- **Healthy**: Fed within last 24 hours
- **Hungry**: 24-48 hours since last feed
- **Critical**: 48-72 hours since last feed
- **Dead**: 72+ hours since last feed

### Visual Effects by State
- **Healthy**: Normal appearance, vibrant colors
- **Hungry**: Slightly dimmed (90% brightness)
- **Critical**: Grayscale filter + pulse animation + reduced brightness
- **Dead**: Full grayscale + low contrast + low brightness
- **Resurrected**: Glowing orbs effect + hue rotation filter

### Resurrection System
- When Chroma dies (after 3 days unfed), a resurrection modal appears
- Secret phrase required: "come back chroma" (case insensitive)
- Resurrection resets streak to 0 and increments resurrection count
- Each resurrection adds visual alterations (glowing eyes effect)

### Emotional Log System
- Logs are generated based on pet state and actions
- Different message tones: content, hungry, critical, dead, resurrected
- Recent logs displayed on dashboard with timestamps
- Logs use muted colors for older/darker states

## Authentication Flow
1. Unauthenticated users see Landing page
2. "Begin Your Journey" button redirects to /api/login
3. Replit OIDC handles authentication
4. After login, redirected to Home page with GameDashboard
5. Logout button in header redirects to /api/logout

## API Routes

### Authentication
- `GET /api/login`: Initiate login flow
- `GET /api/logout`: Logout and clear session
- `GET /api/callback`: OIDC callback handler
- `GET /api/auth/user`: Get current user (returns null if not authenticated)

### Pet Management (Protected)
- `GET /api/pet`: Get pet state, feeding status, and recent logs
- `POST /api/pet/feed`: Feed the pet (once per day)
- `POST /api/pet/resurrect`: Resurrect dead pet with secret phrase
- `GET /api/pet/logs`: Get emotional logs with pagination

## Frontend Components

### Pages
- **Landing**: Marketing page for logged-out users
- **Home**: Main game page with GameDashboard for logged-in users

### Components
- **GameDashboard**: Main game interface with stats and controls
- **PetDisplay**: Animated cat display with state-based visual effects
- **FeedButton**: Interactive feeding button with state handling
- **StatusCard**: Displays pet statistics (streak, health, last fed)
- **EmotionalLogs**: Shows recent emotional log entries
- **RevivalModal**: Resurrection interface with secret phrase input
- **ThemeToggle**: Dark/light mode switcher

## Design System
- **Primary Color**: Chroma Purple (270° 60% 65%)
- **Theme**: Tamagotchi-style with mystical elements
- **Typography**: Custom display fonts for headers
- **Visual Feedback**: CSS filters for state changes, pulse animations for critical state

## Project Structure
```
/client
  /src
    /components    # React components
    /pages        # Page components
    /hooks        # Custom hooks (useAuth)
    /lib          # Utilities (queryClient, authUtils)
/server
  routes.ts       # API routes
  replitAuth.ts   # Authentication setup
  storage.ts      # Database storage interface
  db.ts          # Database connection
/shared
  schema.ts      # Shared types and database schema
```

## Recent Changes (October 10, 2025)
- Integrated Replit Auth for user authentication
- Updated database schema for OIDC authentication
- Created Landing page for logged-out users
- Added logout functionality to GameDashboard
- Fixed unauthorized error handling in mutations
- Configured session cookies for development environment

## Environment Variables
- `DATABASE_URL`: PostgreSQL connection string
- `SESSION_SECRET`: Express session secret
- `REPLIT_DOMAINS`: Comma-separated list of deployment domains
- `ISSUER_URL`: OIDC issuer URL (defaults to https://replit.com/oidc)
- `REPL_ID`: Replit application ID
- `NODE_ENV`: Environment mode (development/production)
