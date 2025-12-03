# AI Code Review Assistant - UX Design Specification

_Created on 2025-12-02 by caiojoao_
_Generated using BMad Method - Create UX Design Workflow v1.0_

---

## Executive Summary

**AI Code Review Assistant** is a standalone web application that provides intelligent, contextual code review feedback through inline AI-powered conversations. Engineers can paste code, select specific lines, and receive targeted suggestions tied directly to their selection.

**Vision:** Inline AI code review that feels native to the development workflow
**Target Users:** Software engineers seeking quick, contextual code feedback
**Core Experience:** Select → Ask → Review → Iterate (all in one focused view)
**Desired Feeling:** Confident (trust the feedback) + Empowered (learning as you go)
**Platform:** Web SPA

**Inspiration:** VS Code density, Cursor's inline chat, GitHub PR review flow, ChatGPT conversational UX

---

## 1. Design System Foundation

### 1.1 Design System Choice

| Layer | Choice | Rationale |
|-------|--------|-----------|
| **UI Components** | shadcn/ui | Tailwind-based, highly customizable, modern minimal aesthetic, great dark mode |
| **Code Editor** | Monaco Editor | VS Code's actual editor - syntax highlighting, selection APIs built-in |
| **State Management** | Kea | Complex state for multiple threads tied to code ranges |
| **Theming** | Dark/Light toggle | Dark as default, matches developer preferences |

**Why This Stack:**
- Monaco gives the actual VS Code editing experience
- shadcn/ui provides UI chrome (panels, buttons, menus) with Linear-like minimalism
- Both support dark/light theming out of the box
- Tailwind makes custom styling fast

---

## 2. Core User Experience

### 2.1 Defining Experience

**"Inline comments meet AI assistant"** - The AI understands surrounding code context, not just the selected portion.

**Core Interaction Loop:**
1. Paste or write code in editor
2. Select specific lines or blocks
3. Request AI feedback via right-click or gutter icon
4. Receive contextual suggestions inline
5. Iterate on conversation within code context

### 2.2 Layout Architecture

**Hybrid Design: Panel Tabs + Expandable Sidebar**

```
┌─────────────────────────────────────────────────────────────────┐
│  [AI] CodeReview        [utils.ts ×] [api.ts ×]           [⚙]  │
├────┬────────────────────────────────┬───────────────────────────┤
│ 📄 │  1  import { useState }...     │ [Threads] History Sug    │
│ 💬 │  2                             │ ─────────────────────     │
│ 📜 │  3  interface User {           │ ┌─────────────────────┐  │
│ ☰  │  4    id: string;              │ │ Lines 5-8        ●  │  │
│    │ •5    name: string;  ████      │ │ You: Add email?     │  │
│    │ •6    email: string; ████      │ │ AI: Use Zod...      │  │
│    │  7  }                          │ └─────────────────────┘  │
│    │                                │ [Ask about code...]      │
└────┴────────────────────────────────┴───────────────────────────┘
```

| Element | Behavior |
|---------|----------|
| **Left Sidebar** | Icon rail (48px) default, expands to 220px with file tree, history, settings |
| **Center** | Monaco editor with gutter indicators |
| **Right Panel** | 400px tabbed panel (Threads / History / Suggestions) |

### 2.3 Interaction Patterns

| Interaction | Method |
|-------------|--------|
| **Trigger AI** | Right-click context menu + gutter icon click |
| **Thread Indicators** | Highlighted background + left border + gutter markers |
| **Thread Navigation** | Click thread → editor scrolls to code |

---

## 3. Visual Foundation

### 3.1 Color System

**Theme: Cursor Cyan** - Fresh, AI-native, distinctive

| Token | Value | Usage |
|-------|-------|-------|
| Background | `#1a1a1a` | Main canvas |
| Elevated | `#242424` | Panels, cards, thread containers |
| Hover | `#2e2e2e` | Interactive states |
| Border | `#333333` | Subtle separators |
| Text | `#e4e4e4` | Primary content |
| Text Muted | `#888888` | Secondary, labels |
| **Primary** | `#22d3ee` | Actions, highlights, AI elements |
| Accent | `#67e8f9` | Links, code keywords |
| Success | `#34d399` | Positive feedback |
| Warning | `#fbbf24` | Caution states |
| Error | `#f87171` | Destructive, errors |

### 3.2 Typography

| Element | Font | Size | Weight |
|---------|------|------|--------|
| UI Text | System (-apple-system, etc.) | 13px | 400 |
| Code | SF Mono, Monaco, monospace | 12px | 400 |
| Headings | System | 14-18px | 600 |
| Labels | System | 11-12px | 500 |

### 3.3 Spacing

- Base unit: 4px
- Scale: 4, 8, 12, 16, 20, 24, 32, 48px
- Component padding: 12-16px
- Card gaps: 12px

**Interactive Visualizations:**
- Color Theme Explorer: [ux-color-themes.html](./ux-color-themes.html)

---

## 4. Design Direction

### 4.1 Chosen Design Approach

**Hybrid: Direction 4 (Panel Tabs) + Direction 6 (Wide Sidebar)**

| Property | Decision |
|----------|----------|
| Layout | Icon sidebar (expandable) + Editor + Tabbed thread panel |
| Density | Balanced (VS Code-like but cleaner) |
| Visual Weight | Minimal borders, subtle elevation |
| Navigation | Tabs for Threads/History/Suggestions |

**Key Characteristics:**
- Clean separation between code and threads
- Tabbed panel for multi-feature access
- Collapsible sidebar for file management when needed
- Gutter markers for thread indicators

**Interactive Mockups:**
- Design Direction Showcase: [ux-design-directions.html](./ux-design-directions.html)

---

## 5. User Journey Flows

### 5.1 Critical User Paths

#### Journey 1: First Use / Paste Code
| Step | User Action | System Response |
|------|-------------|-----------------|
| 1 | Land on app | Empty state with "Paste code or drop file" |
| 2 | Paste code (Cmd+V) | Code populates, syntax highlighted |
| 3 | — | Auto-detect language, show tooltip hint |

#### Journey 2: Create Review Thread
| Step | User Action | System Response |
|------|-------------|-----------------|
| 1 | Select lines 5-8 | Highlight with cyan tint + left border |
| 2 | Right-click or click gutter | Context menu appears |
| 3 | Click "Ask AI" | Thread panel opens, new card created |
| 4 | Type question, press Enter | Loading state, then AI response |

#### Journey 3: Continue Conversation
| Step | User Action | System Response |
|------|-------------|-----------------|
| 1 | Read AI response | "Apply Suggestion" button if code offered |
| 2 | Click Apply | Diff preview modal |
| 3 | Accept/Reject | Code updated or dismissed |
| 4 | Type follow-up | AI responds with thread context |

#### Journey 4: Manage Multiple Threads
| Step | User Action | System Response |
|------|-------------|-----------------|
| 1 | View thread list | Status indicators (Active/Pending/Resolved) |
| 2 | Click thread card | Editor scrolls to code, thread expands |
| 3 | Click "Resolve" | Thread moves to resolved, gutter changes to ✓ |
| 4 | Click "Delete" | Confirmation dialog, then removed |

---

## 6. Component Library

### 6.1 Component Strategy

#### From shadcn/ui (Themed)
| Component | Usage |
|-----------|-------|
| Button | Ask AI, Send, Cancel, Apply |
| Input/Textarea | Thread input |
| Tabs | Threads/History/Suggestions |
| Card | Thread cards |
| DropdownMenu | Context menu |
| Dialog | Diff preview, confirmations |
| Tooltip | Onboarding, gutter info |
| ScrollArea | Thread list |
| Badge | Thread count, status |

#### Custom Components
| Component | Purpose |
|-----------|---------|
| `CodeEditor` | Monaco wrapper with selection events |
| `ThreadCard` | Thread display with messages, code preview |
| `ThreadMessage` | User/AI message with avatar, actions |
| `GutterMarker` | Thread indicators (●○✓) |
| `ContextMenu` | Right-click menu positioned to selection |
| `SidebarNav` | Collapsible icon/wide sidebar |
| `DiffPreview` | Monaco diff editor for apply changes |

#### ThreadCard Anatomy
```
┌─────────────────────────────────────┐
│ [Header] L5-8  ● Active      ✓  ×  │
├─────────────────────────────────────┤
│ [CodePreview] const result = ...   │
├─────────────────────────────────────┤
│ [Messages]                         │
│   👤 You: Question                 │
│   🤖 AI: Response                  │
├─────────────────────────────────────┤
│ [Input] [textarea]          [Send] │
└─────────────────────────────────────┘
```

#### GutterMarker States
- `●` Cyan filled = Active thread
- `○` Cyan hollow = Pending (waiting for AI)
- `✓` Green dimmed = Resolved
- `+` On hover = Create new thread

---

## 7. UX Pattern Decisions

### 7.1 Consistency Rules

#### Button Hierarchy
| Type | Style | Usage |
|------|-------|-------|
| Primary | Solid cyan, dark text | Ask AI, Send, Apply |
| Secondary | Ghost with border | Cancel, Copy |
| Ghost | No border, muted | Dismiss, icons |
| Destructive | Solid red | Delete (in confirmation) |

#### Feedback Patterns
| Type | Pattern |
|------|---------|
| Success | Toast bottom-right, 3s auto-dismiss |
| Error | Toast + inline, persist until dismissed |
| Loading | Inline dots `···` in context |
| AI Thinking | Animated dots in thread |

#### Modal Patterns
| Scenario | Behavior |
|----------|----------|
| Diff preview | Center modal, 80% width, ESC to close |
| Delete confirm | Small dialog, explicit confirm |
| Settings | Slide-out panel from right |

#### Empty States
| State | Message |
|-------|---------|
| No code | "Paste code or drop a file to start" |
| No threads | "Select code and right-click to start a review" |
| No history | "Your review sessions will appear here" |

#### Keyboard Shortcuts
| Shortcut | Action |
|----------|--------|
| `Cmd+Enter` | Send message |
| `Cmd+/` | Toggle sidebar |
| `Escape` | Close modal/deselect |

---

## 8. Responsive Design & Accessibility

### 8.1 Responsive Strategy

| Breakpoint | Width | Adaptation |
|------------|-------|------------|
| Desktop | ≥1280px | Full layout: sidebar + editor + 400px thread panel |
| Laptop | 1024-1279px | Collapsed sidebar, 320px thread panel |
| Tablet | 768-1023px | Thread panel as slide-out drawer |
| Mobile | <768px | "Best on desktop" message |

### 8.2 Accessibility (WCAG 2.1 AA)

#### Color Contrast
| Element | Ratio | Pass |
|---------|-------|------|
| Body text (`#e4e4e4` on `#1a1a1a`) | 12.5:1 | AAA |
| Muted text (`#888888` on `#1a1a1a`) | 5.3:1 | AA |
| Primary button | 12.8:1 | AAA |

#### Keyboard Navigation
- Tab through all interactive elements
- Enter to activate buttons/expand threads
- Arrow keys in menus
- ESC to close modals
- Focus trapped in modals

#### Screen Reader
- Thread panel: `role="complementary"`
- Thread list: `role="list"`
- AI responses: `aria-live="polite"`
- Loading: `aria-busy="true"`

#### Focus Indicators
```css
:focus-visible {
  outline: 2px solid #22d3ee;
  outline-offset: 2px;
}
```

---

## 9. Implementation Guidance

### 9.1 Tech Stack Summary

| Layer | Technology |
|-------|------------|
| Framework | React |
| UI Components | shadcn/ui |
| Code Editor | Monaco Editor |
| State | Kea |
| Styling | Tailwind CSS |
| AI API | Anthropic/OpenAI (swappable) |

### 9.2 Key Implementation Notes

1. **Monaco Selection Events** - Use `onDidChangeCursorSelection` to track selections
2. **Thread-to-Code Binding** - Store line ranges, update on code changes
3. **Gutter Decorations** - Use Monaco's `deltaDecorations` API
4. **AI Streaming** - Stream responses for better UX
5. **State Persistence** - Consider localStorage for session history

### 9.3 Development Priority

1. Core editor with syntax highlighting
2. Selection + context menu
3. Thread panel with single thread
4. AI integration (mock first, then real)
5. Multiple threads
6. Polish (animations, keyboard shortcuts)

---

## Appendix

### Related Documents

- Product Requirements: [Automattic_code_review.docx.pdf](../Automattic_code%20review.docx.pdf)

### Core Interactive Deliverables

- **Color Theme Visualizer**: [ux-color-themes.html](./ux-color-themes.html)
- **Design Direction Mockups**: [ux-design-directions.html](./ux-design-directions.html)

### Version History

| Date | Version | Changes | Author |
|------|---------|---------|--------|
| 2025-12-02 | 1.0 | Initial UX Design Specification | caiojoao |

---

_This UX Design Specification was created through collaborative design facilitation. All decisions were made with user input and documented with rationale._
