# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Mini Stitch is a client-side AI design canvas application that allows users to generate and edit HTML/CSS UI designs through natural language conversations with OpenAI's API. It's a single-file Vue.js 2 application with no build process required.

**Key Technology Stack:**
- Vue.js 2 (CDN-loaded)
- OpenAI Chat Completions API (streaming, with function calling/tools)
- interact.js for drag/resize interactions
- DOMPurify for HTML sanitization
- localForage for persistent storage

## Architecture

### Single-File Application Structure

The entire application is contained in [index.html](index.html), which includes:

1. **CSS Styles** (lines 7-215): Dark-themed UI with CSS Grid layout, design system using CSS variables
2. **HTML Template** (lines 224-350): Vue.js template with v-directives
3. **JavaScript Application** (lines 352-1226): Vue.js instance with all application logic

### Core Components

**Left Panel (Grid Column 1):**
- Toolbar: API key input, model selection, renderer mode selection
- Chat area: Displays conversation with user/assistant/tool messages
- Input area: Text input and send/stop controls

**Right Panel (Canvas):**
- Draggable/resizable design boxes that render HTML in isolated contexts
- Each box has a toolbar with duplicate, export, and delete actions

### Data Flow

1. **User Input** → `send()` method
2. **Chat Loop** → `chatLoop()` orchestrates multi-turn streaming with tool calls
3. **Stream Processing** → `streamOnce()` handles SSE stream from OpenAI, parsing text deltas and tool call deltas
4. **Tool Execution** → Tool call results fed back to model for continued conversation
5. **Design Rendering** → HTML sanitized and rendered in Shadow DOM or sandboxed iframe
6. **Persistence** → State auto-saved to IndexedDB via localForage (debounced 500ms)

### OpenAI Integration Pattern

The app uses OpenAI's streaming API with function calling (tools). The streaming loop:
- Accumulates text content for display (`assistantStreamingText`)
- Buffers tool call arguments as they stream in (`toolBuffers`)
- On `finish_reason: "tool_calls"`, executes tools and continues the loop
- Supports live preview by extracting HTML from streaming markdown code blocks

### Tool Functions

Four tools are exposed to the AI model (lines 354-442):

1. **create_design**: Creates new design box with HTML content
2. **edit_design**: Modifies existing design (full replacement or instruction-based)
3. **replace_string**: Surgical string replacement in a design's HTML
4. **list_designs**: Returns all current designs (for disambiguation)

### Rendering Modes

Two isolation strategies (lines 606-647):

1. **Shadow DOM** (default): Attaches HTML to shadow root for CSS isolation
2. **Sandboxed iframe**: Uses iframe with `sandbox="allow-scripts"` attribute

HTML is sanitized with DOMPurify before rendering, forbidding script/iframe/object/embed/link tags and stripping event handlers.

### State Management

Vue.js reactive data stores:
- `messages[]`: Full chat history (includes system/user/assistant/tool messages)
- `designs[]`: All design boxes with properties (id, name, html, x, y, width, height, z, timestamps, renderMode)
- `apiKey`, `model`, `renderModeDefault`: Persisted to localStorage
- Full state persisted to IndexedDB on every change (debounced)

State restoration happens in `created()` lifecycle hook via `restoreState()`.

### Interaction System

interact.js provides drag and resize for design boxes (lines 686-729):
- Draggable: Updates x/y position in data model
- Resizable: Updates width/height and x/y (when resizing from left/top edges)
- Both trigger `saveState()` on interaction end

## Development Workflow

### Running the Application

This is a static HTML file with no build process:

```bash
# Option 1: Open directly in browser
# Simply open index.html in any modern browser

# Option 2: Use a local server (recommended for testing)
python -m http.server 8000
# Then navigate to http://localhost:8000

# Option 3: If using XAMPP (as suggested by the path)
# Place in htdocs folder and access via http://localhost/AIDesigner/
```

### Making Changes

Since this is a single-file application:
1. Edit [index.html](index.html) directly
2. Refresh browser to see changes
3. No compilation or build step required

### Testing Tool Calls

To test OpenAI tool calling integration:
1. Add an OpenAI API key in the UI (stored in localStorage only)
2. Use prompts that trigger specific tools:
   - "Create a hero section card" → triggers `create_design`
   - "Replace 'Get Started' with 'Try Now' in Hero" → triggers `replace_string`
   - "List all designs" → triggers `list_designs`
   - "Change the background color in Card1 to blue" → triggers `edit_design`

### Debugging Streaming

Key debugging points:
- `streamOnce()` (line 801): Main SSE parsing logic
- `toolBuffers` object: Accumulates tool arguments during streaming
- `assistantStreamingText`: Live preview of assistant's response
- Browser console shows tool execution errors (line 998)

## Important Implementation Details

### Preview System

The app provides live previews in two ways:

1. **Markdown Code Blocks** (line 869): As the assistant streams ```html blocks, content is extracted and rendered in a preview box
2. **Partial Tool Arguments** (line 928): As `create_design` arguments stream in, HTML field is extracted (even if JSON is incomplete) and previewed

Preview box (id: `__preview__`) is replaced with the final design when `create_design` completes, preserving position/size.

### Security Considerations

- **Client-side API key storage**: Keys stored in localStorage are visible in browser. The app includes a warning (lines 1228-1230) that this is dev-only
- **HTML Sanitization**: DOMPurify removes all script tags, event handlers, and dangerous elements
- **Sandbox isolation**: Iframe mode uses sandbox attribute; Shadow DOM provides CSS isolation only

### Message Format Preservation

OpenAI's API requires specific message formats for tool calling. The code preserves `tool_calls` on assistant messages (lines 822-830) so that subsequent tool result messages can reference them via `tool_call_id`.

### Abort Handling

User can stop streaming mid-request:
- `stop()` method (line 765) calls `abort()` on AbortController
- AbortError is caught and suppressed (lines 750-756) as expected behavior
- `_streamLoopGuard` counter (line 772) prevents orphaned loops from continuing

### System Prompt

The system prompt (lines 444-455) instructs the AI to:
- Always use tools for creating/editing designs
- Prefer `replace_string` for small edits
- Call `list_designs` to disambiguate IDs
- Stream HTML preview in code blocks before creating designs
- Generate self-contained, semantic HTML with inline styles

## File Structure

```
AIDesigner/
├── index.html          # Single-file Vue.js application (entire codebase)
├── .git/               # Git repository
└── CLAUDE.md           # This file
```

## API References

- **OpenAI Chat Completions**: Uses streaming endpoint with tools (function calling)
- **Vue.js 2**: Classic Vue.js syntax with Options API
- **interact.js 1.10.27**: Drag and resize interactions
- **DOMPurify 3.1.6**: HTML sanitization
- **localForage 1.10.0**: IndexedDB wrapper for persistence
