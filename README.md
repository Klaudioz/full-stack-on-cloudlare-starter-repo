# Full-Stack SaaS on Cloudflare Workers

A comprehensive smart link shortening SaaS application demonstrating enterprise-level architecture built entirely on Cloudflare's edge platform. This project showcases how to build scalable, cost-effective SaaS solutions using modern microservices patterns.

## 🚀 What We're Building

This application creates a **smart link shortening service** (like Bitly, but smarter) with advanced features:

- **🌍 Geographic Routing**: Route users to different destinations based on their location
- **🤖 AI-Powered Link Analysis**: Background services analyze destination pages for content quality and availability
- **📊 Real-time Analytics**: Comprehensive dashboard with click tracking, geographic data, and performance metrics  
- **💳 SaaS Features**: Complete user authentication, payments integration, and subscription management
- **⚡ Edge Computing**: Blazing fast performance with global edge deployment

## 🏗️ Architecture Overview

This project demonstrates **service-based architecture** with clear separation of concerns:

### 📱 User Application (`apps/user-application`)
**Responsibilities**: UI, Authentication, Basic CRUD Operations
- React frontend with TanStack Router & Query
- tRPC backend for type-safe APIs
- Better Auth for authentication & Stripe for payments
- Real-time WebSocket connections

### ⚙️ Data Service (`apps/data-service`) 
**Responsibilities**: Data Processing, AI Inference, Link Redirects
- Cloudflare Queues for async processing
- Durable Objects for stateful operations & WebSockets
- Workflows for complex multi-step jobs
- Browser Rendering for page analysis
- Workers AI for intelligent content evaluation

### 📦 Data Operations Package (`packages/data-ops`)
**Responsibilities**: Shared Database Logic, Types, Schemas
- Drizzle ORM for type-safe database operations
- Zod schemas for runtime validation
- Shared utilities across services

## 🛠️ Technology Stack

### Frontend
- **React 19** - Modern UI library
- **TanStack Router** - Type-safe routing
- **TanStack Query** - Server state management
- **ShadCN/UI** - Beautiful, accessible components
- **Tailwind CSS v4** - Utility-first styling

### Backend
- **Cloudflare Workers** - Edge computing platform
- **tRPC** - End-to-end type safety
- **Hono** - Fast, lightweight web framework
- **Better Auth** - Modern authentication
- **Drizzle ORM** - Type-safe database operations

### Cloudflare Services
- **Workers AI** - AI inference at the edge
- **Queues** - Async message processing
- **Durable Objects** - Stateful edge computing
- **Workflows** - Multi-step job orchestration
- **Browser Rendering** - Serverless page analysis
- **D1** - Edge-native SQLite database

## 🚦 Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v18+
- [pnpm](https://pnpm.io/) v8+  
- [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/install-and-update/) (included in project dependencies)
- **GitHub account** (required for deployment pipelines)
- **Cloudflare account** (free - sign up at [cloudflare.com](https://cloudflare.com))

### Quick Start

> ⚠️ **Important**: You must **fork** this repository (not just clone) to enable deployment features later in the course.

1. **Fork this repository** to your GitHub account using the Fork button

2. **Clone your fork** (replace `YOUR_USERNAME`):
   ```bash
   git clone https://github.com/YOUR_USERNAME/full-stack-on-cloudlare-starter-repo.git
   cd full-stack-on-cloudlare-starter-repo
   ```

3. **Install all dependencies**:
   ```bash
   pnpm install
   ```

4. **Build the DataOps package** (required first step):
   ```bash
   pnpm run build-package
   ```

5. **Start the frontend application**:
   ```bash
   pnpm run dev-frontend
   ```

6. **Open your browser** to `http://localhost:3000`

You should see the marketing landing page. Click "Get Started for Free" to view the dashboard with sample data.

### Alternative Development Approach

You can also work within individual applications:

```bash
# Navigate to specific app
cd apps/user-application

# Run development server directly
pnpm dev
```

This approach is useful when focusing on a single feature or service.

## 🚀 First Deployment

After getting the development server running, deploy immediately to understand the platform:

### Deploy to Cloudflare Workers

1. **Navigate to user application**:
   ```bash
   cd apps/user-application
   ```

2. **Deploy your application**:
   ```bash
   pnpm run deploy
   ```

3. **First-time authentication**: 
   - Wrangler CLI will open your browser
   - Click "Allow" to grant access to your Cloudflare account

4. **Access your deployed app**:
   - You'll receive a URL like: `user-application.{your-name}.workers.dev`
   - Your app is now live on Cloudflare's global edge network!

### What Happens During Deployment

The deploy command:
- **Builds your application** (`npm run build`) into production-ready bundles
- **Uploads static assets** (JS, HTML, CSS) to Cloudflare's global CDN
- **Deploys Worker code** to handle server-side requests
- **Configures routing** based on your `wrangler.jsonc` settings

### Cloudflare Dashboard

After deployment, visit the [Cloudflare Dashboard](https://dash.cloudflare.com):
- Navigate to **Workers & Pages** 
- Find your `user-application` deployment
- View metrics, logs, deployments, and configure settings
- Monitor real-time analytics and error traces

## ☁️ Cloudflare Resources Overview

This course builds a comprehensive SaaS application using multiple Cloudflare services:

| Resource | Type | Purpose | Used In |
|----------|------|---------|---------|
| **Workers** | Compute | Frontend + Backend services | Both applications |
| **Pages** | Static Hosting | Alternative deployment method | User application |
| **D1** | SQLite Database | Application data storage | Both applications |
| **Queues** | Message Processing | Async background jobs | Data service |
| **Durable Objects** | Stateful Computing | Real-time WebSocket management | Data service |
| **Workflows** | Job Orchestration | Multi-step link analysis | Data service |
| **Workers AI** | AI Inference | Content analysis at edge | Data service |
| **Browser Rendering** | Serverless Browser | Page screenshots/analysis | Data service |
| **KV** | Key-Value Store | Session & cache storage | Both applications |
| **R2** | Object Storage | File & image storage | Data service |
| **Analytics Engine** | Metrics Collection | Usage analytics | Both applications |
| **Custom Domains** | DNS & Routing | Production domains | User application |

### Why This Stack?

- **🌍 Global Edge**: All services run on Cloudflare's 270+ city network
- **💰 Cost Effective**: Pay-per-use pricing with generous free tiers
- **🔗 Integrated**: Services work seamlessly together with minimal configuration
- **⚡ Performance**: Sub-10ms cold starts and built-in CDN
- **🛠️ Developer Experience**: Simple deployment and excellent tooling

## 📚 Course Learning Path

This repository is structured as a **progressive learning experience**:

### 🎯 Core Learning Objectives
- **Service Architecture**: Learn to build scalable microservices with clear boundaries
- **Edge Computing**: Leverage Cloudflare's global edge network for performance
- **Type Safety**: End-to-end TypeScript with tRPC and Drizzle
- **Modern SaaS Patterns**: Authentication, payments, analytics, real-time features
- **AI Integration**: Practical AI features with Workers AI and browser rendering
- **Deployment Mastery**: Deploy early and often to understand the platform deeply
- **Wrangler Expertise**: Master Cloudflare's CLI tool for resource management

### 📖 Course Structure
Each section builds upon the previous, covering:

1. **Project Setup & Architecture** - Monorepo, services, responsibilities
2. **Database Design** - Schema design, Drizzle setup, migrations  
3. **User Interface** - React components, routing, state management
4. **Authentication & Authorization** - Better Auth integration
5. **API Development** - tRPC setup, type-safe APIs
6. **Background Services** - Queues, Durable Objects, Workflows
7. **AI Integration** - Workers AI, browser rendering, content analysis
8. **Real-time Features** - WebSockets, live analytics
9. **Payments Integration** - Stripe setup, subscription management
10. **Deployment & Monitoring** - Production deployment, observability

## 🔧 Development Commands

### Root Level
```bash
pnpm dev-frontend          # Start user application (port 3000)
pnpm dev-data-service      # Start data service with remote bindings  
pnpm build-package         # Build shared packages
```

### User Application (`apps/user-application`)
```bash
pnpm dev                   # Development server
pnpm build                 # Production build + TypeScript check
pnpm test                  # Run tests
pnpm cf-typegen            # Generate Cloudflare types
pnpm deploy                # Deploy to Cloudflare Workers
```

### Data Service (`apps/data-service`)
```bash
pnpm dev                   # Development with remote bindings
pnpm test                  # Run tests  
pnpm cf-typegen            # Generate types
pnpm deploy                # Deploy to Cloudflare Workers
```

## 🌟 Why Cloudflare?

This course demonstrates why Cloudflare is the **perfect middle ground** between managed services (Vercel/Netlify) and cloud platforms (AWS):

### ✅ **Cost Effective**
- Pay per request, not per server
- Generous free tiers for development
- Scales from $0 to enterprise without pricing cliffs

### ⚡ **Performance First**  
- Global edge network (270+ cities)
- Sub-10ms cold starts
- Built-in CDN and caching

### 🛠️ **Developer Experience**
- Simple deployment with Wrangler
- Great documentation and tooling
- TypeScript-first development

### 🔧 **Comprehensive Platform**
- Database, queues, AI, storage all included
- No vendor lock-in concerns
- Scales to millions of requests

## 📁 Project Structure

This monorepo contains **multiple applications and shared packages**:

```
├── apps/                          # Individual applications/services
│   ├── data-service/              # Background processing service
│   │   ├── src/
│   │   │   └── index.ts           # WorkerEntrypoint main service
│   │   ├── test/                  # Vitest tests with Workers pool
│   │   ├── service-bindings.d.ts  # TypeScript service bindings
│   │   ├── wrangler.jsonc         # Cloudflare Worker configuration
│   │   └── vitest.config.mts      # Test configuration
│   └── user-application/          # React frontend + tRPC backend
│       ├── src/                   # React application source
│       │   ├── components/        # UI components (auth, dashboard, home-page, link, ui)
│       │   ├── routes/           # TanStack Router routes
│       │   ├── hooks/            # Custom React hooks (WebSocket, state)
│       │   ├── utils/            # Utility functions and tRPC types
│       │   └── main.tsx          # React application entry point
│       ├── worker/               # Cloudflare Worker backend
│       │   ├── index.ts          # Worker entry point
│       │   └── trpc/             # tRPC router, context & procedures
│       │       ├── router.ts     # Main tRPC router
│       │       ├── context.ts    # Request context creation
│       │       └── routers/      # Individual route handlers
│       ├── vite.config.ts        # Vite build configuration
│       └── wrangler.jsonc        # Cloudflare Worker configuration
├── packages/                      # Shared code across applications
│   └── data-ops/                 # Database operations, schemas, types
│       ├── src/                  # TypeScript source code
│       │   ├── db/               # Database connection and queries
│       │   ├── zod/              # Zod validation schemas
│       │   └── durable-object-helpers/  # Durable Object utilities
│       ├── dist/                 # Built JavaScript (after pnpm run build-package)
│       ├── auth-gen/             # Generated authentication types
│       └── drizzle.config.ts     # Database configuration
├── CLAUDE.md                      # Development guidance (this file)
├── README.md                      # Project documentation
├── package.json                  # Monorepo configuration with workspace scripts
├── pnpm-workspace.yaml           # pnpm workspace configuration
└── pnpm-lock.yaml                # Dependency lock file
```

### Key Structural Notes

- **No root `src/` folder**: Each app maintains its own source directory
- **DataOps Package**: Must be built (`pnpm run build-package`) before apps can import shared code
- **Workspace Dependencies**: Apps reference shared packages with `workspace:*` in package.json
- **Independent Deployment**: Each app can be deployed separately to Cloudflare Workers
- **Generated Files**: `routeTree.gen.ts`, `auth-gen/`, and `dist/` folders are auto-generated

## 🎓 Learning Resources

- **Course Documentation**: Detailed explanations in each section
- **Code Examples**: Complete implementations for each feature
- **Reference Implementation**: [Final course repo](https://github.com/matthew-sessions/cf-services-the-course-playground) 
- **Cloudflare Docs**: [developers.cloudflare.com](https://developers.cloudflare.com/)

## 💡 Getting Help

### Debugging Tips
1. **Check the reference repo** if you get stuck
2. **Use AI assistants** (Claude, Cursor) to debug specific issues
3. **Cloudflare Dashboard** provides excellent debugging tools
4. **Browser DevTools** for frontend issues

### Common Issues

- **"Cannot find module" errors**: Run `pnpm run build-package` to build the DataOps package
- **TypeScript binding errors**: Run `pnpm cf-typegen` after adding Cloudflare resources
- **Build failures**: Ensure all dependencies installed with `pnpm install` from project root
- **Port 3000 in use**: Kill existing processes or change port in vite.config.ts
- **Deployment authentication**: First deploy opens browser - click "Allow" for Wrangler access
- **Deployment failures**: Check authentication with `wrangler whoami`
- **Forking vs Cloning**: Must fork repository for deployment pipelines to work
- **Wrangler configuration**: `wrangler.jsonc` grows complex as features are added

## 🚀 Next Steps

After completing this course, you'll be equipped to:

- **Build Production SaaS Applications** on Cloudflare
- **Architect Scalable Systems** using microservices patterns
- **Integrate AI Features** into real-world applications  
- **Deploy Edge Applications** globally with confidence
- **Optimize for Cost and Performance** at scale

Ready to build the future of web applications? Let's get started! 🎯