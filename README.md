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

## 🔗 Understanding tRPC Routes

**tRPC routes** are the backbone of type-safe communication between your frontend and backend, providing seamless data exchange with full TypeScript integration.

### 🎯 What is a tRPC Route?

A tRPC route is a type-safe API endpoint that:

- **📝 Defines Data Contracts**: Specifies exactly what data goes in and comes out
- **🔒 Ensures Type Safety**: Frontend knows the exact shape of data to expect
- **🧭 Enables Code Navigation**: Click from frontend usage to jump directly to backend code
- **⚡ Handles Business Logic**: Processes requests, queries databases, returns structured data

### 🏗️ tRPC Route Architecture

#### Backend Route Definition
```typescript
// worker/trpc/routers/links.ts
export const linksRouter = router({
  getClicksByCountry: procedure
    .input(z.object({ 
      linkId: z.string() 
    }))
    .output(z.array(z.object({ 
      countryCode: z.string(), 
      count: z.number() 
    })))
    .query(async ({ input, ctx }) => {
      // Database query with full type safety
      const data = await ctx.db.query.getClicksByCountry(input.linkId);
      return data; // TypeScript ensures this matches output schema
    }),
});
```

#### Frontend Usage with TanStack Query
```typescript
// src/components/TopCountriesTable.tsx
export function TopCountriesTable() {
  const { data: clicksByCountry } = api.links.getClicksByCountry.useSuspenseQuery({
    linkId: "example-id" // TypeScript validates this input
  });
  
  return (
    <table>
      {clicksByCountry.map(country => ( // Full autocomplete available
        <tr key={country.countryCode}>
          <td>{country.countryCode}</td>
          <td>{country.count}</td>
        </tr>
      ))}
    </table>
  );
}
```

### 🚀 Why tRPC Routes Are Powerful

#### 1. **End-to-End Type Safety**
- Changes to backend automatically update frontend types
- Compile-time error detection prevents runtime issues
- Refactoring works across the entire stack

#### 2. **Enhanced Developer Experience** 
- **IDE Autocomplete**: Full IntelliSense for API calls
- **Code Navigation**: Command+click jumps from frontend to backend
- **Error Detection**: TypeScript catches mismatches before deployment

#### 3. **Seamless Data Fetching**
- **TanStack Query Integration**: Automatic caching, retry logic, loading states
- **Suspense Support**: Clean loading boundaries with React Suspense
- **Optimistic Updates**: Easy implementation of optimistic UI patterns

#### 4. **Framework Agnostic**
- Works with React, Vue, Svelte, Solid, or any TypeScript frontend
- Same patterns across different project structures
- Easy migration between frontend frameworks

### 🔄 Data Flow Example

```typescript
// 1. User clicks button in React component
<button onClick={() => createLink.mutate({ url: "https://example.com" })}>
  Create Link
</button>

// 2. tRPC route handles the request
createLink: procedure
  .input(z.object({ url: z.string().url() }))
  .mutation(async ({ input, ctx }) => {
    // 3. Database operation with type safety
    const link = await ctx.db.insert(links).values({
      url: input.url,
      shortCode: generateShortCode(),
    });
    return link; // 4. Typed response back to frontend
  })

// 5. Frontend receives typed data and updates UI automatically
```

### 🎯 Best Practices for tRPC Routes

1. **Use Zod Schemas**: Define clear input/output validation
2. **Meaningful Names**: Route names should describe the business operation
3. **Proper Error Handling**: Use tRPC error types for consistent error responses
4. **Context Usage**: Leverage context for database connections, auth, etc.
5. **Route Organization**: Group related routes in separate router files

## 🛣️ TanStack Router: Type-Safe Navigation

**TanStack Router** provides powerful, type-safe routing for React applications with advanced data loading capabilities that eliminate traditional loading states.

### 🗂️ File-Based Routing Convention

TanStack Router uses a file-based approach similar to Next.js but with simpler conventions:

```
src/routes/
├── index.tsx                    // → /
├── app/                         // → /app
│   ├── index.tsx               // → /app (dashboard)
│   └── auth/                   // → /app/auth  
│       └── index.tsx           // → /app/auth (protected dashboard)
└── _authenticated.tsx          // Layout for protected routes
```

**Key Differences from Next.js**:
- ✅ **No `.page.tsx` suffix** - any file becomes a route
- ✅ **Directory = URL structure** - clean, predictable mapping
- ✅ **Index files** represent the base route of their directory

### 🏗️ Route File Structure

Each route file exports a route configuration with data loading and component:

```typescript
// src/routes/app/auth/index.tsx
import { createFileRoute } from '@tanstack/react-router'
import { DashboardPage } from '../../../components/dashboard/DashboardPage'

export const Route = createFileRoute('/app/auth/')({
  // 🚀 Data loading BEFORE component renders
  loader: async ({ context }) => {
    // Prefetch multiple tRPC queries in parallel
    await Promise.all([
      context.queryClient.prefetchQuery(api.links.getAll.queryOptions()),
      context.queryClient.prefetchQuery(api.analytics.getClicksByCountry.queryOptions()),
      context.queryClient.prefetchQuery(api.analytics.getTopDevices.queryOptions()),
    ]);
  },
  // 🎨 React component renders with data already available
  component: DashboardPage,
});
```

### ⚡ Data Loading Magic

#### Traditional Approach (Loading Spinners Everywhere)
```typescript
function Dashboard() {
  const { data: links, isLoading: linksLoading } = api.links.getAll.useQuery();
  const { data: countries, isLoading: countriesLoading } = api.analytics.getClicksByCountry.useQuery();
  
  if (linksLoading || countriesLoading) {
    return <LoadingSpinner />; // 😤 Multiple loading states
  }
  
  return <DashboardContent links={links} countries={countries} />;
}
```

#### TanStack Router Approach (Data Ready Immediately)
```typescript
// 🎯 Route loader prefetches everything
loader: async ({ context }) => {
  await Promise.all([
    context.queryClient.prefetchQuery(api.links.getAll.queryOptions()),
    context.queryClient.prefetchQuery(api.analytics.getClicksByCountry.queryOptions()),
  ]);
},

// ✨ Component renders with data already available
function Dashboard() {
  const { data: links } = api.links.getAll.useSuspenseQuery();
  const { data: countries } = api.analytics.getClicksByCountry.useSuspenseQuery();
  
  // No loading states needed - data is guaranteed to be available!
  return <DashboardContent links={links} countries={countries} />;
}
```

### 🚀 Key Features

#### 1. **Type-Safe Everything**
- **URL Parameters**: Fully typed route parameters
- **Search Params**: Type-safe query string handling
- **Navigation**: Compile-time route validation

#### 2. **Advanced Data Loading**
- **Parallel Prefetching**: Load multiple data sources simultaneously
- **Route-Level Caching**: Intelligent data caching across route changes
- **Error Boundaries**: Built-in error handling for data loading failures

#### 3. **Route Protection & Layouts**
```typescript
// _authenticated.tsx - Layout for protected routes
export const Route = createFileRoute('/_authenticated')({
  beforeLoad: ({ context }) => {
    // Redirect to login if not authenticated
    if (!context.user) {
      throw redirect({ to: '/login' });
    }
  },
  component: AuthenticatedLayout,
});
```

#### 4. **Seamless tRPC Integration**
```typescript
// Perfect integration with tRPC and TanStack Query
loader: async ({ context }) => {
  // Type-safe prefetching of tRPC routes
  await context.queryClient.prefetchQuery(
    api.links.getClicksByCountry.queryOptions({ linkId: params.linkId })
  );
},
```

### 🎯 Why This Approach Wins

#### ✅ **Better User Experience**
- **Instant Page Loads**: Data loads before navigation completes
- **No Loading Spinners**: Components render immediately with data
- **Smooth Transitions**: Seamless navigation between pages

#### ✅ **Better Developer Experience**
- **Type Safety**: Catch routing errors at compile time
- **Predictable Data Flow**: Always know when data is available
- **Simple Patterns**: Consistent approach across all routes

#### ✅ **Better Performance**
- **Parallel Loading**: Multiple data sources load simultaneously
- **Smart Caching**: Avoid unnecessary re-fetching
- **Code Splitting**: Automatic route-based bundle splitting

### 🔗 Integration with Our Stack

TanStack Router works seamlessly with our entire technology stack:

- **🔗 tRPC Routes**: Type-safe API calls with prefetching
- **📊 TanStack Query**: Intelligent caching and state management  
- **🔐 Authentication**: Route-level protection and redirects
- **📱 React Suspense**: Clean loading boundaries and error handling

This combination eliminates the complexity of traditional React data fetching patterns while providing exceptional type safety and performance.

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

### 🧠 Understanding V8 Isolates

Cloudflare Workers run on **V8 Isolates** - the same technology that powers individual browser tabs in Chrome. This architecture provides unique advantages over traditional serverless platforms:

#### Traditional Serverless vs Workers

| Aspect | AWS Lambda | Cloudflare Workers |
|--------|------------|-------------------|
| **Runtime** | Container/VM based | V8 Isolates |
| **Cold Start** | 50-150ms + runtime boot | ~5ms |
| **Billing** | Full request duration | CPU time only |
| **Memory** | 128MB-10GB | 128MB per isolate |
| **Global** | Regional deployment | 270+ edge locations |

#### Key Benefits

- **⚡ Ultra-Fast Cold Starts**: 5ms vs 150ms+ for traditional serverless
- **💸 CPU-Only Billing**: Only pay for active compute, not I/O waiting time
- **🌐 Edge Execution**: Code runs closest to users globally
- **🪶 Lightweight**: No container overhead, direct JavaScript execution

#### Perfect for Modern Apps

V8 Isolates excel at modern application patterns:
- **API-driven apps** with database/external service calls
- **AI-powered features** with LLM API integrations  
- **Real-time applications** with WebSocket connections
- **JAMstack applications** with dynamic server-side logic

**Example**: A 1-second API request that waits 800ms for AI response costs 1 full second on traditional serverless, but only ~8ms of CPU time on Workers.

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

## 📦 Understanding Bundling & Environment Variables

### How Full-Stack Frameworks Work on Cloudflare

Every full-stack framework ultimately compiles down to the same foundation: **a fetch handler that processes HTTP requests**. Whether you're using Next.js, SvelteKit, or TanStack Start, they all create two distinct bundles:

#### 📱 Static Assets Bundle
- React components, CSS, images, JavaScript bundles
- Deployed to **Cloudflare CDN** for lightning-fast global delivery
- Cached automatically, extremely cheap to serve
- **Zero Worker invocations** for asset requests

#### ⚙️ Server Bundle  
- API routes, server components, business logic
- Compiled into a **Worker-compatible fetch handler**
- Runs on Cloudflare's edge, billed only for CPU time
- Handles dynamic requests and data processing

### 💰 Cost Impact: Why This Matters

A typical page load might make 25+ requests for assets, but **only 1 Worker invocation**:
- **25 CDN requests**: Virtually free, served from global cache
- **1 Worker request**: Minimal cost, billed only for active CPU time

This architecture keeps your application lightning-fast and cost-effective at scale.

### 🔐 Environment Variables: The Cloudflare Way

#### ❌ Traditional Node.js Approach (Won't Work)
```javascript
// This fails on Cloudflare Workers
const client = new APIClient(process.env.API_KEY);
```

#### ✅ Cloudflare Worker Approach
```javascript
// Access through Worker context
export default {
  async fetch(request, env, ctx) {
    const apiKey = env.MY_API_KEY;
    const client = new APIClient(apiKey);
    return handleRequest(request, client);
  }
};
```

### 🛠️ Development Environment Setup

#### 1. Create Local Environment File
Create `dev.vars` in your app directory (add to `.gitignore`):
```bash
# apps/user-application/dev.vars
DATABASE_URL=your-local-database-url
STRIPE_SECRET_KEY=sk_test_your_test_key
API_SECRET=your-development-secret
```

#### 2. Generate TypeScript Types
```bash
cd apps/user-application
pnpm cf-typegen
```
This creates type-safe environment variable access in your code.

#### 3. Access in Your Application
Framework-specific examples:

**Hono (Direct Workers)**:
```javascript
app.get('/api/data', (c) => {
  const dbUrl = c.env.DATABASE_URL;
  const apiSecret = c.env.API_SECRET;
  // Use environment variables
});
```

**Next.js on Cloudflare**:
```javascript
import { getRequestContext } from '@cloudflare/next-on-pages';

export default async function handler(req, res) {
  const { env } = getRequestContext();
  const apiKey = env.API_SECRET;
  // Handle request with environment variables
}
```

### 🌍 Production Environment Management

#### Option 1: Per-Worker Secrets
1. Navigate to **Cloudflare Dashboard** → Your Worker → **Settings** → **Variables and Secrets**
2. Add each secret with the **exact same name** as in your `dev.vars`
3. Choose **"Secret"** for sensitive data (encrypted, never visible again)

#### Option 2: Shared Secret Store (Enterprise)
For secrets used across multiple Workers:

1. Create secrets in **Cloudflare Secret Store**
2. Configure in `wrangler.jsonc`:
```json
{
  "secret_store": [
    {
      "binding": "SHARED_SECRETS", 
      "store_id": "your-secret-store-id"
    }
  ]
}
```
3. Access via `env.SHARED_SECRETS.get("SECRET_NAME")`

### 🔄 Framework-Specific Differences

| Framework | Environment Access | Notes |
|-----------|-------------------|-------|
| **Native Workers** | `env` parameter | Direct access in fetch handler |
| **Hono** | `c.env` | Through context object |
| **Next.js** | `getRequestContext()` | Cloudflare-specific helper |
| **TanStack Start** | `process.env` | Uses Nitro runtime compatibility |

### 🔧 TanStack Start & Nitro: The Universal Runtime

**TanStack Start** leverages **Nitro** as its underlying server toolkit, which explains why it can use familiar Node.js patterns on Cloudflare Workers.

#### What is Nitro?

Nitro is a **universal server toolkit** that acts as an abstraction layer between your application code and the deployment runtime:

- **Build-Time Magic**: Compiles your server logic into platform-specific bundles
- **Runtime Translation**: Converts Node.js patterns into Worker-compatible code
- **Cross-Platform**: Same codebase deploys to Workers, Node.js, Vercel, Netlify, etc.

#### How Nitro Enables `process.env` on Workers

```javascript
// This works in TanStack Start applications:
export async function GET() {
  const apiKey = process.env.API_KEY;  // ✅ Nitro handles this
  const client = new APIClient(apiKey);
  return new Response(JSON.stringify(data));
}
```

**Behind the scenes**: Nitro's compilation process ensures `process.env` accesses are transformed into proper Cloudflare environment variable lookups during the build step.

#### Nitro's Ecosystem Impact

- **Powers Multiple Frameworks**: TanStack Start, Nuxt, H3, and others
- **Universal Patterns**: Write once, deploy anywhere philosophy
- **Framework Evolution**: Enables rapid framework development with consistent deployment

### ⚡ Vite: The Build Tool Revolution

**Vite** is revolutionizing how modern web applications are built and deployed, especially on edge platforms like Cloudflare.

#### 🎯 Why Vite is Winning

Vite solves fundamental problems that have plagued JavaScript build tools for years:

- **Lightning Fast**: Instant server start and hot module replacement
- **Universal Standards**: Consistent build process across all frameworks
- **Platform Agnostic**: Same codebase deploys anywhere without vendor lock-in
- **Developer First**: Exceptional development experience with minimal configuration

#### 🌐 Vite's Environment Variable Solution

Vite provides a standardized approach that works consistently across frameworks:

```javascript
// Client-side variables (publicly accessible)
const apiUrl = import.meta.env.VITE_API_URL;

// Server-side variables (secure, framework-specific access)
const secretKey = process.env.SECRET_KEY;  // TanStack Start/Nuxt
const secretKey = c.env.SECRET_KEY;        // Hono
```

#### 📈 Framework Migration to Vite

The entire ecosystem is converging on Vite:

| Framework | Vite Status | Benefits |
|-----------|-------------|----------|
| **SvelteKit** | ✅ Built on Vite | Native Cloudflare support |
| **Nuxt 3** | ✅ Vite by default | Seamless edge deployment |
| **TanStack Start** | 🚧 Migrating fully | Improved Cloudflare compatibility |
| **Next.js** | 🔍 Exploring | Future Vite integration planned |

#### 🔧 Cloudflare Vite Plugin

Cloudflare's official Vite plugin provides:

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import { cloudflare } from '@cloudflare/vite-plugin';

export default defineConfig({
  plugins: [
    cloudflare({
      // Auto-configures Cloudflare-specific settings
      // Generates TypeScript types for bindings
      // Provides local development environment
    })
  ]
});
```

**Key Features**:
- **Zero Configuration**: Works out of the box with Cloudflare
- **Type Safety**: Auto-generates TypeScript types for all Cloudflare resources
- **Local Development**: Accurate simulation of Cloudflare environment
- **Framework Agnostic**: Works with any Vite-based framework

#### 🚀 The Vite + Cloudflare Future

This combination represents the future of web development:

1. **Write Once, Deploy Everywhere**: Same codebase works on Cloudflare, Vercel, Netlify, etc.
2. **Consistent Developer Experience**: Unified tooling across all frameworks
3. **Edge-First Design**: Optimized for modern edge computing platforms
4. **No Vendor Lock-in**: Easy migration between platforms

#### 💡 Framework Selection Strategy

When choosing a framework for Cloudflare development, prioritize those embracing Vite:

- **Best Choice**: SvelteKit, Nuxt (full Vite integration)
- **Good Choice**: TanStack Start (migrating to Vite)
- **Future Proof**: Any framework announcing Vite migration

Frameworks that fully embrace Vite will have the smoothest Cloudflare experience and the best long-term compatibility.

### 🎯 Best Practices

1. **Never commit secrets** - Always add `dev.vars` to `.gitignore`
2. **Use identical naming** - Match local and production environment variable names exactly
3. **Generate types** - Run `pnpm cf-typegen` after adding new variables
4. **Prefer Secret Store** - For shared secrets across multiple services
5. **Framework patterns** - Follow your framework's specific environment access patterns

Ready to build the future of web applications? Let's get started! 🎯