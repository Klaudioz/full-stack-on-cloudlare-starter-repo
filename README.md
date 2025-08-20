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
- [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/install-and-update/)
- Cloudflare account

### Quick Start

1. **Fork this repository** to your GitHub account

2. **Clone your fork**:
   ```bash
   git clone https://github.com/YOUR_USERNAME/full-stack-on-cloudlare-starter-repo.git
   cd full-stack-on-cloudlare-starter-repo
   ```

3. **Install dependencies**:
   ```bash
   pnpm install
   ```

4. **Start development servers**:
   ```bash
   # Terminal 1: Start user application (port 3000)
   pnpm dev-frontend
   
   # Terminal 2: Start data service  
   pnpm dev-data-service
   ```

5. **Open your browser** to `http://localhost:3000`

## 📚 Course Learning Path

This repository is structured as a **progressive learning experience**:

### 🎯 Core Learning Objectives
- **Service Architecture**: Learn to build scalable microservices
- **Edge Computing**: Leverage Cloudflare's global edge network
- **Type Safety**: End-to-end TypeScript with tRPC and Drizzle
- **Modern SaaS Patterns**: Authentication, payments, analytics
- **AI Integration**: Practical AI features in production apps

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

```
├── apps/
│   ├── user-application/          # React frontend + tRPC backend
│   │   ├── src/                   # React application
│   │   │   ├── components/        # UI components
│   │   │   ├── routes/           # TanStack Router routes
│   │   │   └── hooks/            # Custom React hooks
│   │   └── worker/               # Cloudflare Worker backend
│   │       ├── index.ts          # Worker entry point
│   │       └── trpc/             # tRPC router & procedures
│   └── data-service/             # Background processing service
│       ├── src/                  # Service implementation
│       └── test/                 # Service tests
├── packages/
│   └── data-ops/                 # Shared database operations
└── package.json                  # Monorepo configuration
```

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
- **TypeScript Errors**: Run `pnpm cf-typegen` after adding Cloudflare bindings
- **Build Failures**: Ensure all dependencies are installed with `pnpm install`  
- **Deployment Issues**: Check Wrangler authentication with `wrangler whoami`

## 🚀 Next Steps

After completing this course, you'll be equipped to:

- **Build Production SaaS Applications** on Cloudflare
- **Architect Scalable Systems** using microservices patterns
- **Integrate AI Features** into real-world applications  
- **Deploy Edge Applications** globally with confidence
- **Optimize for Cost and Performance** at scale

Ready to build the future of web applications? Let's get started! 🎯