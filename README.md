# LoopIn — Social Networking Platform

> A full-stack social networking web application where users can connect, follow, post, share stories, and chat in real time.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Live Demo](#live-demo)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Database Schema](#database-schema)
- [API Reference](#api-reference)
- [Background Jobs (Inngest)](#background-jobs-inngest)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Environment Variables](#environment-variables)
  - [Installation & Running Locally](#installation--running-locally)
- [Deployment](#deployment)
- [How Key Features Work](#how-key-features-work)

---

## Overview

LoopIn is a LinkedIn/Instagram-inspired social networking platform built with the **MERN stack**. It supports user authentication via Clerk, real-time messaging using Server-Sent Events (SSE), cloud image storage via ImageKit, automated background jobs with Inngest, and email notifications via Nodemailer + Brevo SMTP.

---

## Live Demo

| Service | URL |
|---|---|
| Frontend | Deployed on Vercel |
| Backend | Deployed on Vercel (serverless) |

---

## Features

- 🔐 **Authentication** — Secure sign-up/sign-in powered by Clerk (JWT-based)
- 👤 **User Profiles** — Update name, bio, location, profile picture, and cover photo
- 📰 **Posts & Feed** — Create text, image, or mixed posts; like/unlike posts
- 📸 **Stories** — 24-hour disappearing stories (text, image, or video); auto-deleted by background job
- 🔗 **Connections** — Send/accept connection requests with rate limiting (max 20 per 24 hours)
- 👥 **Follow System** — Follow/unfollow users independently of connections
- 💬 **Real-Time Chat** — One-on-one messaging using Server-Sent Events (SSE), with image support
- 🔔 **Notifications** — In-app toast notifications for incoming messages; email notifications for connection requests and unseen messages
- 🔍 **Discover Users** — Search by name, username, email, or location
- 📧 **Email Reminders** — Automated daily digest of unseen messages; 24-hour connection request reminders

---

## Tech Stack

### Frontend
| Package | Version | Purpose |
|---|---|---|
| React | 19.x | UI framework |
| Vite | 7.x | Build tool & dev server |
| React Router DOM | 7.x | Client-side routing |
| Redux Toolkit | 2.x | Global state management |
| React Redux | 9.x | React bindings for Redux |
| @clerk/clerk-react | 5.x | Authentication (UI + token) |
| Axios | 1.x | HTTP client |
| Tailwind CSS | 4.x | Utility-first styling |
| Lucide React | 0.5x | Icon library |
| React Hot Toast | 2.x | Toast notifications |
| Moment.js | 2.x | Date/time formatting |

### Backend
| Package | Version | Purpose |
|---|---|---|
| Express | 5.x | HTTP server & routing |
| Mongoose | 8.x | MongoDB ODM |
| @clerk/express | 1.x | Clerk middleware for token validation |
| Multer | 2.x | File upload handling |
| ImageKit | 6.x | Cloud image storage & CDN |
| Inngest | 3.x | Background jobs & event-driven functions |
| Nodemailer | 7.x | Email sending via SMTP |
| CORS | 2.x | Cross-Origin Resource Sharing |
| Dotenv | 17.x | Environment variable loading |
| Nodemon | 3.x | Dev server with auto-restart |

### Services
| Service | Purpose |
|---|---|
| MongoDB Atlas | Cloud database |
| Clerk | Authentication & user management |
| ImageKit | Image/video CDN & transformations |
| Inngest | Serverless background job orchestration |
| Brevo (Sendinblue) | SMTP relay for transactional emails |
| Vercel | Frontend & backend deployment |

---

## Project Structure

```
LoopIn/
├── client/                          # React frontend (Vite)
│   ├── public/
│   ├── src/
│   │   ├── api/
│   │   │   └── axios.js             # Axios instance with base URL
│   │   ├── app/
│   │   │   └── store.js             # Redux store configuration
│   │   ├── features/
│   │   │   ├── user/
│   │   │   │   └── userSlice.js     # User state + async thunks
│   │   │   ├── connections/
│   │   │   │   └── connectionSlice.js  # Connections/followers state
│   │   │   └── messages/
│   │   │       └── messagesSlice.js    # Chat messages state
│   │   ├── pages/
│   │   │   ├── Login.jsx            # Auth page
│   │   │   ├── Layout.jsx           # Sidebar + Outlet wrapper
│   │   │   ├── Feed.jsx             # Posts feed
│   │   │   ├── CreatePost.jsx       # Post creation form
│   │   │   ├── Messages.jsx         # Recent conversations list
│   │   │   ├── ChatBox.jsx          # Real-time chat window
│   │   │   ├── Connections.jsx      # Manage connections/followers
│   │   │   ├── Discover.jsx         # Search users
│   │   │   └── Profile.jsx          # User profile page
│   │   ├── components/
│   │   │   ├── Sidebar.jsx
│   │   │   ├── PostCard.jsx
│   │   │   ├── StoriesBar.jsx
│   │   │   ├── StoryModal.jsx
│   │   │   ├── StoryViewer.jsx
│   │   │   ├── ProfileModal.jsx
│   │   │   ├── UserCard.jsx
│   │   │   ├── UserProfileInfo.jsx
│   │   │   ├── RecentMessages.jsx
│   │   │   ├── Notification.jsx
│   │   │   ├── MenuItem.jsx
│   │   │   └── Loading.jsx
│   │   ├── App.jsx                  # Routes + SSE connection setup
│   │   ├── main.jsx                 # Root entry point
│   │   └── index.css
│   ├── vercel.json                  # Vercel SPA rewrite rules
│   ├── vite.config.js
│   └── package.json
│
└── server/                          # Node.js + Express backend
    ├── configs/
    │   ├── db.js                    # MongoDB connection via Mongoose
    │   ├── imageKit.js              # ImageKit SDK configuration
    │   ├── multer.js                # Multer disk storage setup
    │   └── nodeMailer.js            # Nodemailer transporter (Brevo SMTP)
    ├── middlewares/
    │   └── auth.js                  # Clerk-based protect middleware
    ├── models/
    │   ├── User.js                  # User schema
    │   ├── Post.js                  # Post schema
    │   ├── Connection.js            # Connection request schema
    │   ├── Message.js               # Chat message schema
    │   └── Story.js                 # Story schema
    ├── routes/
    │   ├── userRoutes.js
    │   ├── postRoutes.js
    │   ├── storyRoutes.js
    │   └── messageRoutes.js
    ├── controllers/
    │   ├── userController.js
    │   ├── postController.js
    │   ├── storyController.js
    │   └── messageController.js
    ├── inngest/
    │   └── index.js                 # All Inngest background functions
    ├── server.js                    # Express app entry point
    ├── vercel.json                  # Vercel serverless config
    └── package.json
```

---

## Database Schema

### User
```
_id             String (Clerk User ID)     — Primary key, not auto-generated
email           String, required
full_name       String, required
username        String, unique
bio             String, default: "Hey there! I am using LoopIn."
profile_picture String, default: ""
cover_photo     String, default: ""
location        String, default: ""
followers       [String]  — Array of User IDs
following       [String]  — Array of User IDs
connections     [String]  — Array of User IDs (mutual connections)
createdAt       Date (auto)
updatedAt       Date (auto)
```

### Post
```
user            String (ref: User)         — Author
content         String
image_urls      [String]                   — CDN URLs (max 4)
post_type       Enum: text | image | text_with_image
likes_count     [String]                   — Array of User IDs who liked
createdAt       Date (auto)
updatedAt       Date (auto)
```

### Connection
```
from_user_id    String (ref: User)
to_user_id      String (ref: User)
status          Enum: pending | accepted,  default: pending
createdAt       Date (auto)
updatedAt       Date (auto)
```

### Message
```
from_user_id    String (ref: User)
to_user_id      String (ref: User)
text            String (trimmed)
message_type    Enum: text | image
media_url       String
seen            Boolean, default: false
createdAt       Date (auto)
updatedAt       Date (auto)
```

### Story
```
user            String (ref: User)
content         String
media_url       String
media_type      Enum: text | image | video
views_count     [String]                   — Array of User IDs who viewed
background_color String
createdAt       Date (auto)
updatedAt       Date (auto)
```

---

## API Reference

All protected routes require the header:
```
Authorization: Bearer <clerk_jwt_token>
```

### User Routes — `/api/user`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/data` | ✅ | Get current user's data |
| POST | `/update` | ✅ | Update profile (name, bio, location, photos) |
| POST | `/discover` | ✅ | Search users by name, email, username, location |
| POST | `/follow` | ✅ | Follow a user |
| POST | `/unfollow` | ✅ | Unfollow a user |
| POST | `/connect` | ✅ | Send a connection request |
| POST | `/accept` | ✅ | Accept a connection request |
| GET | `/connections` | ✅ | Get connections, followers, following, pending requests |
| POST | `/profiles` | ❌ | Get a user's public profile + their posts |
| GET | `/recent-messages` | ✅ | Get recent messages received |

### Post Routes — `/api/post`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/add` | ✅ | Create a new post (supports up to 4 images) |
| GET | `/feed` | ✅ | Get feed posts from connections + following |
| POST | `/like` | ✅ | Toggle like/unlike on a post |

### Story Routes — `/api/story`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/create` | ✅ | Create a new story (text/image/video) |
| GET | `/get` | ✅ | Get stories from connections + following |

### Message Routes — `/api/message`

| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/:userId` | ❌ | Open SSE stream for real-time messages |
| POST | `/send` | ✅ | Send a message (text or image) |
| POST | `/get` | ✅ | Get chat history with a specific user |

> **Note:** The SSE endpoint does not use the `protect` middleware because the browser's native `EventSource` API cannot send custom headers.

---

## Background Jobs (Inngest)

Inngest handles all asynchronous and scheduled tasks. The webhook endpoint is exposed at `/api/inngest`.

| Function | Trigger | Description |
|---|---|---|
| `sync-user-from-clerk` | `clerk/user.created` | Creates a new User in MongoDB when someone registers via Clerk. Auto-generates a unique username from their email. |
| `update-user-from-clerk` | `clerk/user.updated` | Syncs name, email, and profile picture from Clerk to MongoDB on profile update. |
| `delete-user-with-clerk` | `clerk/user.deleted` | Removes the User document from MongoDB when their Clerk account is deleted. |
| `send-new-connection-request-reminder` | `app/connection-request` | Sends an email immediately when a connection request is sent, then waits 24 hours and sends a reminder if it's still pending. |
| `story-delete` | `app/story.delete` | Waits exactly 24 hours after a story is created using `step.sleepUntil()`, then deletes it from MongoDB. |
| `send-unseen-messages-notification` | Cron: `0 9 * * *` (9 AM EST) | Finds all unread messages daily and emails each recipient their unseen message count with a link to the app. |

---

## Getting Started

### Prerequisites

- Node.js >= 18.x
- npm >= 9.x
- A [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) cluster
- A [Clerk](https://clerk.com) application (for auth)
- An [ImageKit](https://imagekit.io) account (for image storage)
- An [Inngest](https://www.inngest.com) account (for background jobs)
- A [Brevo](https://www.brevo.com) account (for SMTP email)

---

### Environment Variables

#### `server/.env`

```env
# Frontend URL (for email links)
FRONTEND_URL=http://localhost:5173

# MongoDB Atlas connection string
MONGODB_URL=mongodb+srv://<username>:<password>@cluster.mongodb.net

# Inngest
INNGEST_EVENT_KEY=your_inngest_event_key
INNGEST_SIGNING_KEY=your_inngest_signing_key

# Clerk
CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...

# ImageKit
IMAGEKIT_PUBLIC_KEY=public_...
IMAGEKIT_PRIVATE_KEY=private_...
IMAGEKIT_URL_ENDPOINT=https://ik.imagekit.io/your_id

# Email (Brevo SMTP)
SENDER_EMAIL=your@email.com
SMTP_USER=your_brevo_smtp_user
SMTP_PASS=your_brevo_smtp_password
```

#### `client/.env`

```env
# Your backend URL
VITE_BASE_URL=http://localhost:4000

# Clerk publishable key
VITE_CLERK_PUBLISHABLE_KEY=pk_test_...
```

---

### Installation & Running Locally

#### 1. Clone the repository

```bash
git clone https://github.com/your-username/loopin.git
cd loopin
```

#### 2. Install server dependencies

```bash
cd server
npm install
```

#### 3. Install client dependencies

```bash
cd ../client
npm install
```

#### 4. Set up environment variables

Create `server/.env` and `client/.env` files using the templates above.

#### 5. Start the backend server

```bash
cd server
npm run server       # Uses nodemon for auto-reload
```

The server runs at **http://localhost:4000**

#### 6. Start the Inngest dev server (in a new terminal)

```bash
npx inngest-cli@latest dev -u http://localhost:4000/api/inngest
```

This is required to run background jobs locally. The Inngest dev server listens for events and invokes your local functions.

#### 7. Start the frontend (in a new terminal)

```bash
cd client
npm run dev
```

The frontend runs at **http://localhost:5173**

---

## Deployment

Both `client` and `server` are independently deployed to **Vercel**.

### Frontend Deployment

The `client/vercel.json` contains a catch-all rewrite rule so React Router handles all routes on the client side:

```json
{
  "rewrites": [{ "source": "/(.*)", "destination": "/" }]
}
```

Set `VITE_BASE_URL` and `VITE_CLERK_PUBLISHABLE_KEY` in Vercel environment variables for the frontend project.

### Backend Deployment

The `server/vercel.json` uses `@vercel/node` to deploy Express as a serverless function:

```json
{
  "version": 2,
  "builds": [{ "src": "server.js", "use": "@vercel/node" }],
  "routes": [{ "src": "/(.*)", "dest": "server.js" }]
}
```

Set all server `.env` variables in Vercel environment variables for the backend project.

> **After deployment:** Update `FRONTEND_URL` in the backend and `VITE_BASE_URL` in the frontend to the deployed URLs. Also update the Clerk dashboard with your production domain and the Inngest dashboard with your production server URL.

---

## How Key Features Work

### Authentication (Clerk)

1. User signs up/in through the Clerk-hosted UI component
2. Clerk fires a `clerk/user.created` webhook to the Inngest endpoint
3. Inngest's `syncUserCreation` function creates the user in MongoDB using the Clerk user ID as `_id`
4. On every API call, the frontend attaches the Clerk JWT in the `Authorization` header
5. `clerkMiddleware()` decodes the token and `protect` middleware validates it — making `req.auth()` available in controllers

### Real-Time Messaging (SSE)

1. When the app loads and a user is authenticated, `App.jsx` opens a persistent `EventSource` connection to `/api/message/:userId`
2. The server stores the `res` object in a `connections` in-memory map keyed by userId
3. When User B sends a message to User A via `POST /api/message/send`:
   - The message is saved to MongoDB
   - The sender receives a `200 OK` immediately
   - The server then pushes the message to User A's open SSE stream via `connections[userId].write(...)`
4. In `App.jsx`, `eventSource.onmessage` fires — if the user is on that chat page, the message is added to Redux; otherwise a toast notification is shown

### 24-Hour Story Auto-Delete

1. When a story is created, the controller fires `inngest.send({ name: 'app/story.delete', data: { storyId } })`
2. The Inngest `deleteStory` function runs `step.sleepUntil(now + 24 hours)` — it pauses without blocking any thread
3. After 24 hours, Inngest resumes and calls `Story.findByIdAndDelete(storyId)`

### Image Uploads (Multer + ImageKit)

1. `Multer` middleware receives the file and saves it to the OS temp directory
2. The controller reads the file as a buffer using `fs.readFileSync(file.path)`
3. The buffer is uploaded to **ImageKit** via their SDK
4. ImageKit returns a `filePath`; we build an optimized CDN URL with transformations (auto quality, WebP format, max width)
5. The CDN URL is stored in MongoDB — files are never stored on the server permanently

---

## Author

Built by **Ajith Kumar Guntipalli**
