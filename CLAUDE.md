# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Next.js 15 UI application for Deep Agents - generic AI agents capable of handling tasks of varying complexity. The UI connects to LangGraph servers running deep agent instances.

## Development Commands

```bash
# Install dependencies
npm install

# Run development server with Turbopack
npm run dev

# Build for production
npm run build

# Start production server
npm run start

# Run linting
npm run lint
```

## Environment Configuration

Create a `.env.local` file with the following variables:

```env
# For local development
NEXT_PUBLIC_DEPLOYMENT_URL="http://127.0.0.1:2024"
NEXT_PUBLIC_AGENT_ID=<your agent ID from langgraph.json>

# Additional for production deployment on LangGraph Platform
NEXT_PUBLIC_LANGSMITH_API_KEY=<langsmith-api-key>
```

## Architecture Overview

### Core Stack
- **Framework**: Next.js 15.4.6 with React 19
- **Language**: TypeScript with strict mode
- **Styling**: SCSS modules + Tailwind CSS v4
- **State Management**: URL state via `nuqs`, React hooks for local state
- **API Client**: LangChain/LangGraph SDK for agent communication

### Project Structure

```
src/
├── app/                       # App router pages and components
│   ├── components/           # Page-specific components
│   │   ├── ChatInterface/    # Main chat UI with agent interaction
│   │   ├── ChatMessage/      # Individual message rendering
│   │   ├── FileViewDialog/   # File viewer modal
│   │   ├── MarkdownContent/  # Markdown renderer
│   │   ├── SubAgentPanel/    # Sub-agent details panel
│   │   ├── SubAgentIndicator/# Sub-agent status indicator
│   │   ├── TasksFilesSidebar/# Tasks and files management sidebar
│   │   ├── ThreadHistorySidebar/# Thread history management
│   │   └── ToolCallBox/      # Tool invocation display
│   ├── hooks/
│   │   └── useChat.ts        # Main chat hook using LangGraph SDK
│   ├── types/
│   │   └── types.ts          # TypeScript interfaces
│   └── styles/               # Global styles and SCSS variables
├── components/ui/            # Reusable UI components (Radix UI based)
├── lib/                      # Utility functions and client setup
│   ├── client.ts            # LangGraph client factory
│   └── environment/         # Environment configuration
└── providers/               # React context providers
    └── Auth.tsx            # Authentication context
```

### Key Architectural Patterns

1. **Component Organization**: Each component has its own folder with `.tsx` and `.module.scss` files for co-location of logic and styles.

2. **State Management**: 
   - Thread ID persisted in URL query params using `nuqs`
   - Thread state (todos, files) fetched from LangGraph server
   - Local UI state managed with React hooks

3. **Real-time Streaming**: Uses LangGraph SDK's streaming capabilities to handle agent responses, tool calls, and sub-agent interactions.

4. **Authentication**: LangSmith API key authentication handled via Auth provider context.

### Key Components

- **ChatInterface**: Central hub managing chat messages, agent streaming, and sub-agent selection
- **useChat Hook**: Encapsulates LangGraph SDK streaming logic, message handling, and state updates
- **TasksFilesSidebar**: Displays current todos and files from agent state
- **SubAgentPanel**: Shows detailed sub-agent execution when selected

### Agent Communication Flow

1. User sends message → `useChat` hook → LangGraph SDK stream
2. Agent processes → Streams back messages, tool calls, state updates
3. State updates (todos/files) → Trigger UI updates via callbacks
4. Sub-agent invocations → Displayed in UI with expandable details

### Styling Approach

- Component-specific styles use SCSS modules (`.module.scss`)
- Shared UI components use Tailwind utilities
- Theme variables defined in `_variables.scss`
- Radix UI components wrapped with custom styling

## Important Dependencies

- `@langchain/langgraph-sdk`: Core SDK for agent communication
- `@radix-ui/*`: Headless UI components
- `nuqs`: URL query state management
- `react-markdown` + `react-syntax-highlighter`: For rendering agent responses
- `tailwind-merge` + `clsx`: Utility for conditional classes