# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a smart link shortening SaaS application (similar to Bitly but with geo-routing and AI-powered link evaluation) built entirely on Cloudflare's edge platform using a monorepo structure. The application demonstrates enterprise-level system architecture using microservices patterns while maintaining cost-effectiveness and scalability.

### Core Features
- **Smart Link Shortening**: Create short links with custom routing logic
- **Geographic Routing**: Route users to different destinations based on their location
- **AI-Powered Link Evaluation**: Background services analyze destination pages for content quality and availability
- **Real-time Analytics Dashboard**: Track clicks, geographic data, and link performance
- **Authentication & Payments**: Complete SaaS features with user management and billing integration

### Architecture Philosophy
The project follows a **service-based architecture** where responsibilities are clearly separated between user-facing operations and data-intensive background processes. This approach scales better than monolithic full-stack applications and aligns with modern SaaS development patterns.

### Applications Structure
1. **user-application**: React frontend + tRPC backend for user-facing operations
2. **data-service**: Dedicated Cloudflare Worker for data processing, AI inference, and link redirects
3. **data-ops**: Shared package for database operations, types, and schemas

## Architecture

### Monorepo Structure
- **No root `src/` folder**: Multiple applications/services in single repository
- **Apps Directory**: `apps/` contains two main services (user-application, data-service)
- **Packages Directory**: `packages/` contains shared code (DataOps package with Zod schemas, database queries, generic functions)
- **Workspace Management**: Uses pnpm workspaces with `workspace:*` protocol
- **Package Building**: DataOps package must be built (`pnpm run build-package`) before apps can use it
- **Scaling Pattern**: Add new services to `apps/`, shared utilities to `packages/`

### Service Responsibilities

#### User Application
**Responsibilities**: Routing, Authentication, CRUD Operations
- **Frontend Stack**: React + TanStack Router + TanStack Query + ShadCN UI
- **Backend Stack**: tRPC + Hono for API routing
- **Authentication**: Better Auth with Stripe payments integration
- **Real-time Features**: WebSocket proxying to data service
- **Purpose**: Handle user-facing operations, basic database CRUD, request validation

#### Data Service  
**Responsibilities**: Data Processing, Long-Running Tasks, Link Redirects
- **Compute Primitives**: Cloudflare Queues, Durable Objects, Workflows
- **AI & Rendering**: Workers AI for inference, Browser Rendering for page analysis
- **Triggers**: API requests, cron schedules, queue events
- **Purpose**: Heavy data operations, background processing, intelligent link routing

#### Data Operations Package
**Responsibilities**: Schema Management, Database Queries, Shared Types
- **ORM**: Drizzle for type-safe database operations
- **Validation**: Zod schemas for runtime type checking
- **Purpose**: Shared code, reusable database functions, type definitions

## Development Commands

### Root Level
```bash
pnpm dev-frontend          # Start user application development server
pnpm dev-data-service      # Start data service development
pnpm build-package         # Build shared packages (when available)
```

### User Application (apps/user-application)
```bash
pnpm dev                   # Start development server on port 3000
pnpm build                 # Build for production and run TypeScript check
pnpm test                  # Run vitest tests
pnpm cf-typegen            # Generate Cloudflare Worker types
pnpm deploy                # Build and deploy to Cloudflare Workers
```

### Data Service (apps/data-service)
```bash
pnpm dev                   # Start with remote bindings
pnpm start                 # Start local development
pnpm test                  # Run vitest tests
pnpm cf-typegen            # Generate Cloudflare types
pnpm deploy                # Deploy to Cloudflare Workers
```

## Key File Locations

### User Application (`apps/user-application/`)
- **Worker Entry**: `worker/index.ts`
- **tRPC Router**: `worker/trpc/router.ts`
- **tRPC Context**: `worker/trpc/context.ts`
- **tRPC Routers**: `worker/trpc/routers/`
- **React Entry**: `src/main.tsx`
- **Router Config**: `src/router.tsx`
- **Routes**: `src/routes/`
- **Components**: `src/components/` (auth, dashboard, home-page, link, ui)
- **Hooks**: `src/hooks/` (WebSocket, state management)
- **Utilities**: `src/utils/trpc-types.ts`

### Data Service (`apps/data-service/`)
- **Entry Point**: `src/index.ts`
- **Tests**: `test/`
- **Configuration**: `wrangler.jsonc`, `vitest.config.mts`

### DataOps Package (`packages/data-ops/`)
- **Database**: `src/db/database.ts`
- **Zod Schemas**: `src/zod/` (links.ts, queue.ts)
- **Durable Objects**: `src/durable-object-helpers/`
- **Built Output**: `dist/` (JavaScript compiled from TypeScript)
- **Auth Generated**: `auth-gen/auth.ts`

## Configuration Files

### Build Configuration
- **Vite Config**: `apps/user-application/vite.config.ts` - Uses Cloudflare Vite plugin
- **TypeScript**: Standard configs in each app
- **Tailwind**: Uses new Tailwind CSS v4 with Vite plugin

### Cloudflare Configuration
- **User App Wrangler**: `apps/user-application/wrangler.jsonc` - Configured as SPA with assets binding
- **Data Service Wrangler**: `apps/data-service/wrangler.jsonc` - Uses nodejs_compat flag
- **Type Bindings**: Each app has `service-bindings.d.ts` and `worker-configuration.d.ts`

## Testing

Both applications use Vitest for testing:
- **User Application**: Uses jsdom environment with React Testing Library
- **Data Service**: Uses Cloudflare Vitest Workers pool for Worker testing

## Type Safety

The project uses comprehensive TypeScript typing:
- **tRPC**: Provides end-to-end type safety between frontend and backend
- **Cloudflare Bindings**: Custom type definitions for Worker environment
- **Service Bindings**: Extend base Env interface for additional bindings

## Data Flow Architecture

### Typical User Request Flow
1. **Client Request** → User Application (React frontend)
2. **Authentication Layer** → Better Auth validates user session
3. **tRPC Backend** → Handles CRUD operations, proxies complex requests
4. **Database Operations** → Uses DataOps package for type-safe queries
5. **Background Processing** → Complex operations pushed to Data Service via queues

### Data Service Triggers
- **API Requests**: Direct service-to-service communication
- **Cron Jobs**: Scheduled tasks for link health checks, analytics processing  
- **Queue Events**: Asynchronous processing of link analysis, AI inference
- **WebSocket Events**: Real-time updates for dashboard analytics

## Initial Setup Workflow

### Prerequisites Setup
1. **GitHub Account**: Required for deployment pipelines and automated deployments
2. **Fork Repository**: Fork the starter repo to your GitHub account (do not just clone)
3. **Clone Your Fork**: `git clone https://github.com/YOUR_USERNAME/repo-name.git`
4. **IDE Setup**: Cursor recommended (any IDE works)

### First-Time Setup Commands
```bash
# 1. Install all dependencies across monorepo
pnpm install

# 2. Build DataOps package (required before starting apps)
pnpm run build-package

# 3. Start frontend application
pnpm run dev-frontend
# OR navigate to specific app: cd apps/user-application && pnpm dev
```

## Development Workflow Options

### Monorepo Level (Recommended for Course)
- Run commands from project root
- Use `pnpm run dev-frontend` and `pnpm run dev-data-service`
- Manages all services from single terminal location

### Individual App Level
- Navigate into specific app directory: `cd apps/user-application`
- Run `pnpm dev` directly in app folder
- Useful when focusing on single feature development
- Can open individual apps in separate IDE instances

## Development Workflow

1. **Start Development**: Use `pnpm dev-frontend` and `pnpm dev-data-service` concurrently
2. **Package Updates**: Run `pnpm run build-package` when updating DataOps package
3. **Service Binding Updates**: Run `pnpm cf-typegen` when adding Cloudflare resources
4. **Type Safety**: Update `service-bindings.d.ts` files for new bindings
5. **Testing**: Run `pnpm test` in each service before deployment
6. **Deployment**: Deploy services independently using their respective deploy commands

## Deployment Workflow

### First-Time Deployment Setup
1. **Create Cloudflare Account**: Sign up for free at cloudflare.com
2. **Navigate to Workers & Pages**: Focus on compute workers section in dashboard
3. **CLI Authentication**: First `pnpm deploy` opens browser for Wrangler CLI access approval

### Deployment Process
```bash
# From apps/user-application/ directory
pnpm run deploy
```

This command:
1. **Builds Application**: Runs `npm run build` to compile and bundle frontend
2. **Bundles for Workers**: Creates Worker-compatible JavaScript bundle
3. **Uploads Static Assets**: Deploys JS, HTML, CSS files to Cloudflare CDN globally
4. **Uploads Server Code**: Deploys Worker runtime code for HTTP request handling
5. **Provides URL**: Returns `{name}.{account}.workers.dev` deployment URL

### Wrangler Configuration (`wrangler.jsonc`)
- **Deployment Instructions**: Tells Cloudflare how to deploy your code
- **Entry Point Specification**: Defines where application starts (`worker/index.ts`)
- **Application Naming**: Provides unique identifier for Cloudflare tracking
- **Resource Configuration**: Grows complex as more Cloudflare features are added
- **Observability**: Enables logging and error tracking when set to true

### Cloudflare Dashboard Features
- **Deployments**: View deployment history and status
- **Metrics**: Analytics on requests, sub-requests, usage patterns  
- **Error Traces**: Debug application issues with detailed logs
- **Bindings**: Manage asset bindings and service connections
- **Domains & Routes**: Configure custom domains (covered later in course)
- **Settings**: Application configuration management

### Deployment Philosophy

This course emphasizes **deployment-first learning**:
- Deploy early and often to understand Cloudflare platform
- Familiarity with deployment process reduces learning barriers
- Cloudflare Dashboard becomes primary debugging tool
- Production deployment experience from day one
- CLI and Dashboard provide complementary management approaches

## Scaling Philosophy

This architecture follows **microservices patterns** that allow:
- **Independent Scaling**: Each service scales based on its specific demands
- **Technology Flexibility**: Services can use different tech stacks as needed  
- **Cost Optimization**: Pay only for compute resources actually used
- **Maintainability**: Clear boundaries make debugging and updates easier

## Cloudflare Resources Created

This course creates and configures multiple Cloudflare resources across both applications:

| Resource | Type | Application | Purpose | Configuration |
|----------|------|-------------|---------|---------------|
| **user-application** | Worker | User App | Frontend + tRPC backend | SPA with ASSETS binding |
| **data-service** | Worker (EntryPoint) | Data Service | Background processing | nodejs_compat enabled |
| **ASSETS** | Static Assets | User App | Frontend files (JS/CSS/HTML) | Global CDN deployment |
| **D1 Database** | SQLite Database | Both | Application data storage | Edge-native SQL database |
| **Queues** | Message Queue | Data Service | Async link processing | Background job processing |
| **Durable Objects** | Stateful Computing | Data Service | WebSocket management | Real-time analytics state |
| **Workflows** | Multi-step Jobs | Data Service | Complex job orchestration | Sequential task execution |
| **Workers AI** | AI Inference | Data Service | Link content analysis | Edge AI processing |
| **Browser Rendering** | Serverless Browser | Data Service | Page screenshot/analysis | Headless browser automation |
| **KV Store** | Key-Value Store | Both | Session/cache storage | Global edge storage |
| **R2 Storage** | Object Storage | Data Service | File/image storage | S3-compatible storage |
| **Analytics Engine** | Analytics | Both | Usage metrics collection | Real-time analytics |
| **Custom Domain** | DNS/Routing | User App | Production domain | Custom domain routing |

### Resource Dependencies

- **D1 Database**: Shared between both applications via service bindings
- **Queues**: Data service consumes, user app produces via tRPC
- **Durable Objects**: Manage WebSocket connections for real-time dashboard
- **Workflows**: Orchestrate link analysis pipeline (fetch → render → analyze → store)

### Deployment Notes

- All resources are defined in respective `wrangler.jsonc` files
- Service bindings connect user-application to data-service resources
- Resource creation happens automatically during first deployment
- Observability enabled for both Workers for logging and debugging

## Important Notes

- The project uses compatibility_date "2025-06-17" for both Workers
- User application is configured as a Single Page Application (SPA)
- Data service uses WorkerEntrypoint pattern for service-to-service communication
- All configuration uses JSONC format for better commenting support
- Observability is enabled for both Workers