# CLAUDE.md

This file provides guidance for Claude Code when working with this project.

## Quick Navigation

- [Project Overview](#project-overview)
- [Project Structure](#project-structure)
- [Tech Stack](#tech-stack)
- [Setup & Installation](#setup--installation)
- [Architecture](#architecture)
- [Development Workflow](#development-workflow)
- [API Endpoints](#api-endpoints)
- [File Structure](#file-structure)

## Project Overview

AI Chat Panel is a full-stack solution providing real-time AI conversations with SSE streaming, tool execution, and session management. Built on Next.js 15 with React 18, featuring Claude and OpenAI model support.

## Project Structure

```
chat-panel-fullstack/
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── api/chat/          # SSE chat API endpoints
│   │   ├── layout.tsx         # Root layout
│   │   ├── page.tsx           # Home page
│   │   └── globals.css        # Global styles
│   ├── components/            # React components
│   │   ├── chat-demo.tsx      # Chat panel demo
│   │   └── header.tsx         # App header
│   ├── lib/                   # Core libraries
│   │   ├── ai-client.ts       # AI SDK wrapper
│   │   ├── claude-client.ts   # Claude-specific client
│   │   ├── tools.ts           # Tool executor
│   │   ├── session-store.ts   # Session management
│   │   ├── permission-registry.ts # Permission handling
│   │   ├── config-helper.ts   # Configuration utilities
│   │   ├── platform.ts        # Platform detection
│   │   └── claude-types.ts    # TypeScript types
│   └── chat-panel-core/       # Custom component library
├── .env.local.example         # Environment variables template
├── package.json               # Dependencies
├── tsconfig.json              # TypeScript configuration
├── tailwind.config.ts         # Tailwind CSS configuration
└── next.config.ts             # Next.js configuration
```

## Tech Stack

### Frontend
- **Framework**: Next.js 15 (App Router)
- **UI Library**: React 18
- **Language**: TypeScript (strict mode)
- **Styling**: Tailwind CSS 4.0
- **Icons**: Lucide React, Hugeicons
- **Animations**: Motion (Framer Motion)
- **Markdown**: Streamdown (with CJK, code, math, mermaid support)

### Backend
- **API**: Next.js API Routes (Route Handlers)
- **Streaming**: Server-Sent Events (SSE)
- **AI Integration**: 
  - Anthropic Claude SDK (@anthropic-ai/sdk, @anthropic-ai/claude-agent-sdk)
  - OpenAI SDK (openai)
- **Session Management**: In-memory (session-store.ts)

### Development
- **Build Tool**: Next.js 15
- **Type Checking**: TypeScript 5
- **Linting**: ESLint 9 with Next.js config
- **Package Manager**: npm

## Setup & Installation

### Prerequisites
- Node.js >= 18.0.0
- npm

### Installation Steps

1. **Install dependencies**
   ```bash
   npm install
   ```

2. **Configure environment variables**
   ```bash
   cp .env.local.example .env.local
   ```
   
   Edit `.env.local` with your API keys:
   - `AI_PROVIDER`: Choose 'anthropic' or 'openai'
   - `ANTHROPIC_API_KEY`: Your Anthropic API key
   - `OPENAI_API_KEY`: Your OpenAI API key (if using OpenAI)
   - Optional: Custom base URLs and model names

3. **Start development server**
   ```bash
   npm run dev
   ```
   Visit http://localhost:3000

### Environment Configuration Priority

1. `.env.local` file (highest priority)
2. System environment variables
3. `~/.claude/settings.json` (local CLI config)

## Architecture

### Application Flow

```
User Input (Component)
    ↓
POST /api/chat
    ↓
AI Client Wrapper (lib/ai-client.ts)
    ↓
Claude/OpenAI SDK
    ↓
SSE Stream Response
    ↓
Tool Execution (if requested)
    ↓
Permission Request (if required)
    ↓
User Confirmation
    ↓
Tool Result → AI → Response
```

### Key Components

1. **AI Client Layer** (`lib/ai-client.ts`)
   - Unified interface for Claude and OpenAI
   - Handles streaming responses
   - Manages tool execution flow

2. **Claude Client** (`lib/claude-client.ts`)
   - Anthropic-specific implementation
   - Advanced tool handling
   - Permission management

3. **Session Management** (`lib/session-store.ts`)
   - In-memory session storage
   - Message history persistence
   - Multi-session support

4. **Tool System** (`lib/tools.ts`)
   - Built-in tools: time, calculator, web search
   - Extensible tool architecture
   - Permission-based execution

5. **Permission Registry** (`lib/permission-registry.ts`)
   - Tracks pending tool executions
   - Manages user approval workflow

## Development Workflow

### Code Style Guidelines

- **TypeScript**: Strict mode enabled, use explicit types
- **Components**: Functional components with hooks
- **Styling**: Tailwind CSS utility classes, avoid inline styles
- **Imports**: Use path aliases (`@/*`, `@/chat-panel-core/*`)
- **Error Handling**: Always handle errors with proper logging

### Adding New Features

1. **New API Endpoint**: Create in `src/app/api/[endpoint]/route.ts`
2. **New Component**: Add to `src/components/`
3. **New Tool**: 
   - Add definition in `lib/tools.ts`
   - Implement execution logic
   - Configure permission requirements

### Testing

```bash
# Type checking
npm run typecheck

# Linting
npm run lint

# Build
npm run build

# Start production server
npm start
```

### Common Commands

```bash
npm run dev          # Start development server
npm run build        # Build for production
npm run start        # Start production server
npm run lint         # Run ESLint
npm run typecheck    # Run TypeScript type check
```

## API Endpoints

### POST /api/chat
**Purpose**: SSE streaming chat endpoint

**Request Body**:
```json
{
  "session_id": "string",
  "content": "string",
  "model": "string (optional)"
}
```

**Response Events**:
- `text` - AI text delta
- `tool_use` - Tool invocation started
- `tool_result` - Tool execution result
- `permission_request` - User approval required
- `status` - Status update
- `result` - Final response
- `error` - Error information
- `done` - Stream completed

### POST /api/chat/permission
**Purpose**: Confirm tool execution permission

**Request Body**:
```json
{
  "permissionRequestId": "string",
  "decision": "allow" | "deny"
}
```

## File Structure

### Core Libraries (`src/lib/`)

- **ai-client.ts**: Main AI client abstraction layer
- **claude-client.ts**: Anthropic Claude-specific implementation
- **tools.ts**: Built-in tools (time, calculator, search)
- **session-store.ts**: Session and message management
- **permission-registry.ts**: Permission request tracking
- **config-helper.ts**: Environment configuration utilities
- **platform.ts**: Platform detection (desktop/mobile)
- **claude-types.ts**: TypeScript type definitions

### Components (`src/components/`)

- **chat-demo.tsx**: Main chat panel demo component
- **header.tsx**: Application header component

### API Routes (`src/app/api/`)

- **chat/route.ts**: Main chat endpoint with SSE streaming
- **chat/permission/route.ts**: Permission confirmation endpoint

### Component Library (`src/chat-panel-core/`)

Custom reusable chat panel components with:
- Message rendering
- Input handling
- Streaming display
- Tool execution UI

## Important Notes

### API Key Security
- Never commit `.env.local` to version control
- Use `.env.local.example` as template
- Support for local CLI config (`~/.claude/settings.json`)

### Streaming Implementation
- Uses Server-Sent Events (SSE) for real-time responses
- Client-side: EventSource or fetch with streaming
- Server-side: Next.js Route Handlers with streaming response

### Tool Execution Flow
1. AI requests tool use
2. Server checks permission requirements
3. If required, send `permission_request` event
4. User confirms via `/api/chat/permission`
5. Execute tool and return result
6. AI continues with tool result

### Session Management
- Sessions stored in-memory (consider database for production)
- Each session maintains message history
- Support for multiple concurrent sessions

### TypeScript Configuration
- Path aliases configured for clean imports
- Strict mode enabled for type safety
- Module resolution: bundler mode for Next.js 15

## Deployment

### Vercel (Recommended)
1. Push to GitHub
2. Import project in Vercel
3. Configure environment variables
4. Deploy

### Docker
```bash
docker build -t chat-panel-fullstack .
docker run -p 3000:3000 -e ANTHROPIC_API_KEY=your_key chat-panel-fullstack
```

### Environment Variables Required
- `AI_PROVIDER` (anthropic or openai)
- `ANTHROPIC_API_KEY` or `OPENAI_API_KEY`
- Optional: Custom base URLs and model names

## Extensions & Customization

### Adding Custom Tools
1. Define tool in `lib/tools.ts`
2. Implement execution logic
3. Add to tool list in AI client
4. Configure permission requirements

### Model Configuration
Switch models by modifying `.env.local`:
- Claude: `ANTHROPIC_MODEL=claude-3-5-sonnet-20241022`
- OpenAI: `OPENAI_MODEL=gpt-4o`

### UI Customization
Modify `src/components/chat-demo.tsx` ChatPanel configuration:
```tsx
<ChatPanel
  sessionId="custom-session"
  config={{
    title: "Your Title",
    description: "Your Description",
    models: [...],
    defaultModel: "your-model",
  }}
/>
```

## Troubleshooting

### API Connection Issues
- Verify API keys in `.env.local`
- Check network connectivity
- Review base URL configuration

### SSE Streaming Problems
- Ensure client properly handles EventSource
- Check for CORS issues
- Verify server streaming implementation

### TypeScript Errors
- Run `npm run typecheck`
- Check `tsconfig.json` paths configuration
- Ensure all dependencies installed

## Additional Resources

- **Anthropic API Docs**: https://docs.anthropic.com/
- **OpenAI API Docs**: https://platform.openai.com/docs/
- **Next.js Docs**: https://nextjs.org/docs
- **Tailwind CSS**: https://tailwindcss.com/docs
