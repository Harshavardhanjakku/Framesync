# FrameSync

---

## Overview

FrameSync is a full‑stack media collaboration platform for organizations. Teams upload and manage media, comment and annotate in real time, and securely share across orgs. Authentication and authorization are handled by Keycloak; the UI is built with Next.js/React, the API with Express, and real‑time sync uses Socket.IO.

## Features

- **Authentication & SSO**: Keycloak integration with session middleware
- **Organizations & Users**: Organization scoping, invites, and user management
- **Media Upload & Serving**: Upload media and serve from `/uploads`
- **Real‑time Collaboration**: Live comments and annotations via Socket.IO rooms
- **Secure Sharing**: Share media to internal or external users
- **Polyglot Storage**: PostgreSQL for metadata; MongoDB for comments/annotations
- **Modern Frontend**: Next.js + React client with Axios and Socket.IO client
- **Dockerized Dev Stack**: Keycloak, Postgres, MongoDB, and Mongo Express

## Tech Stack

- **Frontend**: Next.js 15, React 19, Tailwind CSS (config in `client/tailwind.config.js`)
- **Backend**: Node.js, Express
- **Auth**: Keycloak (via `keycloak-connect`)
- **Databases**: PostgreSQL (`pg`) and MongoDB (`mongoose`)
- **Realtime**: Socket.IO
- **Tooling**: Axios, Multer, UUID, ESLint, Nodemon
- **Infra**: Docker Compose for local services

## Project Structure

```
.
├─ client/                # Next.js app (pages, components, lib)
│  ├─ pages/              # Routes: index, media, users, organizations, upload, shared
│  ├─ components/         # UI components (Navbar, MediaCard, etc.)
│  └─ lib/                # API client, socket, keycloak setup
├─ server/                # Express API + Socket.IO
│  ├─ index.js            # App entry; routes + sockets
│  ├─ routes/             # Users, media, comments, annotations, orgs, invites
│  └─ config/             # DB clients (Postgres, Mongo), Keycloak init
├─ docker/                # docker-compose and resources (Keycloak themes)
└─ storage/               # Media storage (uploads)
```

> Note: Some folders (e.g., `server/routes`, `server/config`) are referenced by code and expected to exist.

## Getting Started

### Prerequisites

- Node.js 18+
- Docker Desktop (for local Keycloak/Postgres/Mongo stack)

### Installation

```bash
# From repository root
npm install           # installs root deps if any (optional)

# Install client and server deps
cd client && npm install && cd ..
cd server && npm install && cd ..
```

### Run Dev Services (Docker)

```bash
# Start Keycloak, Postgres, MongoDB, and Mongo Express
cd docker
docker compose up -d
cd ..
```

Services will be available at:
- Keycloak: `http://localhost:8080`
- Postgres: `localhost:5432`
- MongoDB: `mongodb://localhost:27017`
- Mongo Express: `http://localhost:8081`

### Environment Configuration

Create `.env` files as needed.

Backend (`server/.env`):

```env
PORT=5000
# PostgreSQL
PGHOST=localhost
PGPORT=5432
PGDATABASE=framesyncdb
PGUSER=framesyncuser
PGPASSWORD=framesyncpass

# MongoDB
MONGODB_URI=mongodb://admin:admin123@localhost:27017/framesync?authSource=admin

# Keycloak
KEYCLOAK_REALM=framesync
KEYCLOAK_CLIENT_ID=framesync-api
KEYCLOAK_CLIENT_SECRET=change-me
KEYCLOAK_AUTH_SERVER_URL=http://localhost:8080
SESSION_SECRET=someSecret
```

Frontend (`client/.env.local`):

```env
NEXT_PUBLIC_API_BASE_URL=http://localhost:5000
NEXT_PUBLIC_KEYCLOAK_URL=http://localhost:8080
NEXT_PUBLIC_KEYCLOAK_REALM=framesync
NEXT_PUBLIC_KEYCLOAK_CLIENT_ID=framesync-web
```

> Adjust values to match your Keycloak realm and clients. The client uses `localStorage` for a bearer token in `client/lib/api.js`.

## Running Locally

```bash
# Start the backend API + Socket.IO
cd server
npm run dev

# In a separate terminal, start the Next.js client
cd client
npm run dev
```

- Client: `http://localhost:3000`
- API: `http://localhost:5000`

## API Endpoints

Base URL: `http://localhost:5000`

```http
GET /                 # Health/info
GET /db-test          # Database connectivity test

# Feature areas (mounted paths)
/users                # Users
/media                # Media upload/metadata
/comments             # Comments (realtime broadcast events: new-comment)
/annotations          # Annotations (realtime broadcast events: new-annotation)
/media-shared         # Media sharing
/org-invites          # Organization invitations
/organizations        # Organizations management
/uploads/*            # Static media serving
```

> Route files under `server/routes` define specific verbs/params (not shown here). Socket rooms join via `join-media` with `mediaId`.


## Deployment

- **Docker Compose (local/dev)**: provided in `docker/docker-compose.yml`.
- **Vercel (client)**: build with `npm run build` in `client/`, set env vars.
- **Render/Heroku/Fly.io (server)**: run `npm start` in `server/`, set env vars for Postgres/Mongo/Keycloak.
- **Keycloak**: use the included container; configure realm, clients, roles, and users.

## Contributing

1. Fork the repo
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Commit your changes: `git commit -m "feat: add your feature"`
4. Push to the branch: `git push origin feat/your-feature`
5. Open a Pull Request

## Acknowledgements

- Keycloak team for excellent open-source IAM
- Next.js and React communities
- Socket.IO, PostgreSQL, MongoDB maintainers
- Icons: Font Awesome
