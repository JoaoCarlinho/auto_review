# Architecture - AI Code Review Assistant

## Executive Summary

A standalone React SPA providing intelligent, contextual code review through inline AI-powered conversations. Built with Vite + React + TypeScript, Monaco Editor for code editing, Kea for state management, and shadcn/ui for components. AI integration uses Vercel AI SDK with Ollama for local development and AWS Bedrock for production deployment.

## Project Initialization

First implementation story should execute:

```bash
# 1. Create Vite project
npm create vite@latest code-review-assistant -- --template react-ts
cd code-review-assistant

# 2. Install Tailwind CSS v4
npm install tailwindcss @tailwindcss/vite

# 3. Initialize shadcn/ui
npx shadcn@latest init

# 4. Install core dependencies
npm install @monaco-editor/react kea kea-localstorage kea-router
npm install ai ollama-ai-provider @ai-sdk/amazon-bedrock
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom @playwright/test
```

This establishes the base architecture with:
- Vite build tooling
- React 19 + TypeScript
- Tailwind CSS v4
- shadcn/ui component library
- Monaco Editor
- Kea state management

---

## Decision Summary

| Category | Decision | Version | Affects Epics | Rationale |
|----------|----------|---------|---------------|-----------|
| Build Tool | Vite | 6.x | All | Fast dev server, native ES modules |
| Framework | React | 19.x | All | Component model, hooks ecosystem |
| Language | TypeScript | 5.x | All | Type safety, IDE support |
| Styling | Tailwind CSS | 4.x | All | Utility-first, design system alignment |
| UI Components | shadcn/ui | latest | All | Customizable, Tailwind-based |
| Code Editor | Monaco Editor | @monaco-editor/react | 1, 2, 5 | VS Code engine, selection APIs |
| State Management | Kea | 3.x | All | Complex state, logic separation |
| AI SDK | Vercel AI SDK | 5.x | 4 | Provider abstraction, streaming |
| AI (Dev) | Ollama | ollama-ai-provider@1.2 | 4 | Local development, no API costs |
| AI (Prod) | AWS Bedrock | @ai-sdk/amazon-bedrock@3.0 | 4 | Production deployment, IAM auth |
| Testing | Vitest + Playwright | latest | All | Vite-native, E2E coverage |
| Persistence | localStorage | kea-localstorage | 3, 6 | Browser-local, offline support |

---

## Project Structure

```
code-review-assistant/
├── public/
│   └── favicon.svg
├── src/
│   ├── components/           # React components
│   │   ├── ui/               # shadcn/ui components (auto-generated)
│   │   ├── editor/           # Epic 1: Core Editor
│   │   │   ├── CodeEditor.tsx
│   │   │   ├── EditorTheme.ts
│   │   │   └── LanguageDetector.ts
│   │   ├── selection/        # Epic 2: Selection & Context
│   │   │   ├── SelectionHighlight.tsx
│   │   │   ├── ContextMenu.tsx
│   │   │   └── GutterHoverIcon.tsx
│   │   ├── threads/          # Epic 3: Thread Management
│   │   │   ├── ThreadPanel.tsx
│   │   │   ├── ThreadCard.tsx
│   │   │   ├── ThreadMessage.tsx
│   │   │   └── ThreadInput.tsx
│   │   ├── markers/          # Epic 5: Thread-Code Binding
│   │   │   ├── GutterMarker.tsx
│   │   │   └── CodeHighlight.tsx
│   │   └── layout/           # Epic 6: Layout & Navigation
│   │       ├── Sidebar.tsx
│   │       ├── TabPanel.tsx
│   │       └── Header.tsx
│   ├── logic/                # Kea logic (state management)
│   │   ├── editorLogic.ts    # Code content, language, Monaco ref
│   │   ├── selectionLogic.ts # Current selection state
│   │   ├── threadsLogic.ts   # Thread CRUD, messages, status
│   │   ├── aiLogic.ts        # AI provider, streaming state
│   │   └── uiLogic.ts        # Sidebar, tabs, theme
│   ├── lib/                  # Utilities & services
│   │   ├── ai/
│   │   │   ├── provider.ts   # Ollama/Bedrock provider factory
│   │   │   ├── context.ts    # Build AI context from selection
│   │   │   └── prompts.ts    # System prompts for code review
│   │   ├── monaco/
│   │   │   ├── setup.ts      # Monaco configuration
│   │   │   └── decorations.ts # Thread decoration helpers
│   │   ├── storage.ts        # localStorage helpers
│   │   └── errors.ts         # Error types & handlers
│   ├── hooks/                # Custom React hooks
│   │   ├── useMonacoSelection.ts
│   │   └── useStreamingResponse.ts
│   ├── types/                # TypeScript types
│   │   ├── thread.ts
│   │   ├── message.ts
│   │   └── editor.ts
│   ├── App.tsx               # Main layout composition
│   ├── main.tsx              # Entry point + Kea provider
│   └── index.css             # Tailwind imports + theme vars
├── tests/
│   ├── e2e/                  # Playwright tests
│   │   └── review-flow.spec.ts
│   └── setup.ts              # Vitest setup
├── .env.local                # Dev environment (Ollama)
├── .env.production           # Prod environment (Bedrock)
├── tailwind.config.ts
├── vite.config.ts
├── tsconfig.json
└── package.json
```

---

## Epic to Architecture Mapping

| Epic | Components | Logic | Libraries |
|------|------------|-------|-----------|
| 1. Core Editor Foundation | `editor/*` | `editorLogic.ts` | `@monaco-editor/react` |
| 2. Selection & Context | `selection/*` | `selectionLogic.ts` | Monaco selection API |
| 3. Thread Management | `threads/*` | `threadsLogic.ts` | `kea-localstorage` |
| 4. AI Integration | — | `aiLogic.ts` | `ai`, `ollama-ai-provider`, `@ai-sdk/amazon-bedrock` |
| 5. Thread-Code Binding | `markers/*` | `threadsLogic.ts` | Monaco decorations API |
| 6. Layout & Navigation | `layout/*` | `uiLogic.ts` | shadcn/ui Tabs, Sheet |
| 7. Polish & Accessibility | All components | — | — |

---

## Technology Stack Details

### Core Technologies

| Layer | Technology | Purpose |
|-------|------------|---------|
| Runtime | Node.js 20 LTS | Development & build |
| Framework | React 19 | UI components |
| Language | TypeScript 5.x (strict) | Type safety |
| Bundler | Vite 6.x | Dev server, production build |
| Styling | Tailwind CSS 4.x | Utility-first CSS |
| Components | shadcn/ui | Pre-built accessible components |
| Editor | Monaco Editor | Code editing, syntax highlighting |
| State | Kea 3.x | Centralized state management |
| AI | Vercel AI SDK 5.x | Provider abstraction, streaming |

### Integration Points

```
┌─────────────────────────────────────────────────────────────┐
│                        React App                            │
├─────────────┬─────────────┬─────────────┬──────────────────┤
│   Monaco    │    Kea      │  shadcn/ui  │   Vercel AI SDK  │
│   Editor    │   State     │  Components │                  │
├─────────────┴──────┬──────┴─────────────┼──────────────────┤
│                    │                    │                  │
│  Selection Events  │  Thread State      │  AI Streaming    │
│  Decorations API   │  localStorage      │  Provider Switch │
│                    │                    │                  │
└────────────────────┴────────────────────┴──────────────────┘
                                                    │
                              ┌─────────────────────┴───────┐
                              │                             │
                         Development                   Production
                         ┌─────────┐                 ┌───────────┐
                         │ Ollama  │                 │   AWS     │
                         │ (local) │                 │  Bedrock  │
                         └─────────┘                 └───────────┘
```

---

## Implementation Patterns

### Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Components | PascalCase | `ThreadCard.tsx` |
| Logic files | camelCase + "Logic" | `threadsLogic.ts` |
| Hooks | "use" prefix | `useMonacoSelection.ts` |
| Types/Interfaces | PascalCase | `interface Thread {}` |
| Constants | SCREAMING_SNAKE | `const MAX_THREADS = 50` |
| CSS | Tailwind utilities | `className="flex gap-2"` |
| Tests | Co-located `.test.tsx` | `ThreadCard.test.tsx` |

### Component Pattern

```typescript
// Standard component structure
import { cn } from '@/lib/utils';
import { useValues, useActions } from 'kea';
import { threadsLogic } from '@/logic/threadsLogic';

interface ThreadCardProps {
  threadId: string;
  className?: string;
}

export function ThreadCard({ threadId, className }: ThreadCardProps) {
  const { threads } = useValues(threadsLogic);
  const { setActiveThread } = useActions(threadsLogic);

  const thread = threads.find(t => t.id === threadId);
  if (!thread) return null;

  return (
    <div className={cn("rounded-lg border p-4", className)}>
      {/* ... */}
    </div>
  );
}
```

### Kea Logic Pattern

```typescript
// Standard logic structure
import { kea, path, actions, reducers, selectors } from 'kea';
import type { threadsLogicType } from './threadsLogicType';

export const threadsLogic = kea<threadsLogicType>([
  path(['logic', 'threads']),

  actions({
    createThread: (selection: Selection) => ({ selection }),
    addMessage: (threadId: string, message: Message) => ({ threadId, message }),
    setThreadStatus: (threadId: string, status: ThreadStatus) => ({ threadId, status }),
  }),

  reducers({
    threads: [[] as Thread[], {
      createThread: (state, { selection }) => [...state, newThread(selection)],
    }],
  }),

  selectors({
    activeThread: [(s) => [s.threads, s.activeThreadId],
      (threads, id) => threads.find(t => t.id === id)],
  }),
]);
```

### Import Alias Pattern

```typescript
// ALL imports use @/ prefix
import { Button } from '@/components/ui/button';
import { threadsLogic } from '@/logic/threadsLogic';
import { buildReviewContext } from '@/lib/ai/context';
import type { Thread } from '@/types/thread';
```

---

## Consistency Rules

### Code Organization

- **One component per file** - Named export matching filename
- **Logic co-location** - Related logic in same directory
- **Type files** - Shared types in `src/types/`, local types inline
- **Test co-location** - `*.test.tsx` next to source file

### Error Handling

```typescript
// Centralized error types
export class AIError extends Error {
  constructor(
    message: string,
    public readonly type: 'timeout' | 'rate_limit' | 'provider_error',
    public readonly retryable: boolean = true,
    public readonly retryAfterMs?: number
  ) {
    super(message);
    this.name = 'AIError';
  }
}

// Thread-level error state
interface Thread {
  id: string;
  status: 'active' | 'pending' | 'resolved';
  error?: {
    message: string;
    retryable: boolean;
  };
}
```

### Logging Strategy

```typescript
// Development: console methods
// Production: structured JSON (future enhancement)
const logger = {
  info: (msg: string, data?: object) => console.info(`[INFO] ${msg}`, data),
  warn: (msg: string, data?: object) => console.warn(`[WARN] ${msg}`, data),
  error: (msg: string, data?: object) => console.error(`[ERROR] ${msg}`, data),
};
```

---

## Data Architecture

### Core Types

```typescript
// types/thread.ts
export interface Thread {
  id: string;                    // crypto.randomUUID()
  decorationId: string;          // Monaco decoration reference
  status: 'active' | 'pending' | 'resolved';
  messages: Message[];
  codeSnapshot: string;          // Original selected code
  language: string;
  createdAt: number;             // Date.now()
  error?: ThreadError;
}

// types/message.ts
export interface Message {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  createdAt: number;
}

// types/editor.ts
export interface Selection {
  startLine: number;
  endLine: number;
  startColumn: number;
  endColumn: number;
  text: string;
}
```

### State Shape (Kea)

```typescript
// Persisted to localStorage
interface PersistedState {
  code: string;
  language: string;
  threads: Thread[];
  activeThreadId: string | null;
}

// Ephemeral (not persisted)
interface EphemeralState {
  currentSelection: Selection | null;
  streamingThreadId: string | null;
  sidebarExpanded: boolean;
  activeTab: 'threads' | 'history' | 'suggestions';
}
```

### Thread-Code Binding

Monaco decorations track code positions automatically:

```typescript
// Create decoration when thread is created
const decorationId = editor.deltaDecorations([], [{
  range: new monaco.Range(startLine, 1, endLine, 1),
  options: {
    isWholeLine: true,
    className: 'thread-highlight',
    glyphMarginClassName: 'thread-gutter-marker',
    stickiness: monaco.editor.TrackedRangeStickiness.GrowsOnlyWhenTypingAfter,
  }
}])[0];

// Get current range when needed
const currentRange = editor.getModel()?.getDecorationRange(decorationId);
```

---

## API Contracts

### AI Provider Interface

```typescript
// lib/ai/provider.ts
import { ollama } from 'ollama-ai-provider';
import { bedrock } from '@ai-sdk/amazon-bedrock';

export function getModel() {
  const provider = import.meta.env.VITE_AI_PROVIDER;

  if (provider === 'ollama') {
    return ollama(import.meta.env.VITE_OLLAMA_MODEL || 'codellama');
  }

  return bedrock(import.meta.env.VITE_BEDROCK_MODEL || 'anthropic.claude-sonnet-4-20250514-v1:0');
}
```

### AI Context Builder

```typescript
// lib/ai/context.ts
export function buildReviewContext(params: {
  selectedCode: string;
  fullCode: string;
  language: string;
  startLine: number;
  endLine: number;
  userQuestion: string;
  previousMessages?: Message[];
}): string {
  return `
You are an expert code reviewer. Review the selected code and provide specific, actionable feedback.

Language: ${params.language}

Full file context:
\`\`\`${params.language}
${params.fullCode}
\`\`\`

Selected code (lines ${params.startLine}-${params.endLine}):
\`\`\`${params.language}
${params.selectedCode}
\`\`\`

User question: ${params.userQuestion}

Provide clear, constructive feedback focused on the selected code.
`.trim();
}
```

---

## Security Architecture

| Concern | Approach |
|---------|----------|
| API Keys | None in frontend; Bedrock uses IAM roles |
| User Code | Stays in browser localStorage; sent to AI provider only |
| XSS | React's built-in escaping; no dangerouslySetInnerHTML |
| CORS | Ollama local; Bedrock via AWS SDK handles auth |

**Production (AWS):**
- App deployed as static assets to S3 + CloudFront
- Bedrock calls via Lambda function with IAM role
- No API keys exposed to browser

---

## Performance Considerations

| Concern | Strategy |
|---------|----------|
| Monaco bundle size | Dynamic import, load on demand |
| AI streaming | SSE via Vercel AI SDK, chunked rendering |
| Re-renders | Kea selectors, React.memo for thread cards |
| localStorage | Debounced writes, prune old threads |

```typescript
// Lazy load Monaco
const CodeEditor = lazy(() => import('@/components/editor/CodeEditor'));

// Debounce localStorage writes
const debouncedPersist = debounce((state) => {
  localStorage.setItem('app-state', JSON.stringify(state));
}, 1000);
```

---

## Deployment Architecture

### Development

```
┌──────────────┐     ┌──────────────┐
│   Browser    │────▶│  Vite Dev    │
│              │     │  Server      │
└──────────────┘     └──────────────┘
       │
       ▼
┌──────────────┐
│   Ollama     │
│  (localhost) │
└──────────────┘
```

### Production (AWS)

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Browser    │────▶│  CloudFront  │────▶│     S3       │
│              │     │    (CDN)     │     │  (Static)    │
└──────────────┘     └──────────────┘     └──────────────┘
       │
       ▼
┌──────────────┐     ┌──────────────┐
│    Lambda    │────▶│   Bedrock    │
│  (AI Proxy)  │     │   (Claude)   │
└──────────────┘     └──────────────┘
```

---

## Development Environment

### Prerequisites

- Node.js 20 LTS
- npm 10+
- Ollama installed locally (for dev)
- AWS CLI configured (for prod deployment)

### Environment Variables

```bash
# .env.local (development)
VITE_AI_PROVIDER=ollama
VITE_OLLAMA_MODEL=codellama
VITE_OLLAMA_BASE_URL=http://localhost:11434

# .env.production (AWS)
VITE_AI_PROVIDER=bedrock
VITE_BEDROCK_MODEL=anthropic.claude-sonnet-4-20250514-v1:0
VITE_API_ENDPOINT=https://api.yourapp.com
```

### Setup Commands

```bash
# Clone and install
git clone <repo>
cd code-review-assistant
npm install

# Start Ollama (separate terminal)
ollama run codellama

# Start dev server
npm run dev

# Run tests
npm run test        # Vitest unit/component
npm run test:e2e    # Playwright E2E

# Build for production
npm run build
```

---

## Architecture Decision Records (ADRs)

### ADR-001: Vercel AI SDK for Provider Abstraction

**Context:** Need to support multiple AI providers (Ollama dev, Bedrock prod).

**Decision:** Use Vercel AI SDK with provider plugins.

**Rationale:** Single API for streaming, built-in React hooks, official AWS Bedrock support.

### ADR-002: Monaco Decorations for Position Tracking

**Context:** Threads bound to code ranges must survive edits.

**Decision:** Use Monaco's `deltaDecorations` with `TrackedRangeStickiness`.

**Rationale:** Monaco handles position tracking natively; no manual offset recalculation.

### ADR-003: Kea for State Management

**Context:** Complex state with multiple threads, selections, and UI state.

**Decision:** Use Kea with localStorage plugin.

**Rationale:** Explicit logic separation, built-in persistence, TypeScript support.

### ADR-004: localStorage for Persistence

**Context:** PRD requires standalone app, mentions history as bonus feature.

**Decision:** Use browser localStorage via kea-localstorage.

**Rationale:** No backend required, works offline, sufficient for single-user MVP.

### ADR-005: Vite over Next.js

**Context:** Need a React SPA, no SSR requirements.

**Decision:** Use Vite with React template.

**Rationale:** Faster dev server, simpler deployment, no server-side complexity.

---

_Generated by BMAD Decision Architecture Workflow v1.3.2_
_Date: 2025-12-02_
_For: caiojoao_
