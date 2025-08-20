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
- Uses pnpm workspaces for dependency management
- Apps are located in `apps/` directory
- Shared packages (when added) go in `packages/` directory
- Workspace dependencies use `workspace:*` protocol

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

### User Application
- **Worker Entry**: `apps/user-application/worker/index.ts`
- **tRPC Router**: `apps/user-application/worker/trpc/router.ts`
- **tRPC Routers**: `apps/user-application/worker/trpc/routers/`
- **React Entry**: `apps/user-application/src/main.tsx`
- **Routes**: `apps/user-application/src/routes/`
- **Components**: `apps/user-application/src/components/`
- **Hooks**: `apps/user-application/src/hooks/`

### Data Service
- **Entry Point**: `apps/data-service/src/index.ts`
- **Tests**: `apps/data-service/test/`

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

## Development Workflow

1. **Start Development**: Use `pnpm dev-frontend` and `pnpm dev-data-service` concurrently
2. **Service Binding Updates**: Run `pnpm cf-typegen` when adding Cloudflare resources
3. **Type Safety**: Update `service-bindings.d.ts` files for new bindings
4. **Testing**: Run `pnpm test` in each service before deployment
5. **Deployment**: Deploy services independently using their respective deploy commands

## Scaling Philosophy

This architecture follows **microservices patterns** that allow:
- **Independent Scaling**: Each service scales based on its specific demands
- **Technology Flexibility**: Services can use different tech stacks as needed  
- **Cost Optimization**: Pay only for compute resources actually used
- **Maintainability**: Clear boundaries make debugging and updates easier

## Important Notes

- The project uses compatibility_date "2025-06-17" for both Workers
- User application is configured as a Single Page Application (SPA)
- Data service uses WorkerEntrypoint pattern for service-to-service communication
- All configuration uses JSONC format for better commenting support
- Observability is enabled for both Workers