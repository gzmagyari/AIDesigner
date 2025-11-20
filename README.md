# Mini Stitch

> **The world's first AI design canvas in a single HTML file.** No installation, no backend, no build process. Just download and run.

A revolutionary client-side AI design canvas application that allows you to generate and edit HTML/CSS UI designs through natural language conversations with OpenAI's API—all contained in **one standalone HTML file**.

![License](https://img.shields.io/badge/license-MIT-blue.svg)

## What Makes This Unique?

**This is the ONLY AI design tool that:**
- ✨ Runs entirely in a **single HTML file** (~1200 lines)
- 🚀 Requires **zero installation** or dependencies to download
- 💻 Works **100% client-side** with no backend server needed
- 📦 Has **no build process, no npm, no bundler**
- 🎯 Just download `index.html` and open it in your browser

While other AI design tools require complex setups, Docker containers, Node.js servers, or cloud platforms, Mini Stitch proves that a fully-featured AI canvas application can run from a single, self-contained HTML file you can open directly in your browser.

## Features

**All of this in ONE HTML file:**

- **Natural Language Design**: Create and edit UI components by simply describing what you want
- **Live Preview**: See designs render in real-time as the AI generates them
- **Interactive Canvas**: Drag, resize, and arrange multiple design components on a canvas
- **AI Function Calling**: Implements OpenAI's streaming tool/function calling API
- **Multiple Rendering Modes**: Choose between Shadow DOM or sandboxed iframe rendering
- **Persistent Storage**: All designs and conversations automatically saved to IndexedDB
- **Truly Portable**: Copy the file anywhere, send it via email, host it anywhere—it just works

## Technology Stack

**Everything loaded via CDN (no local dependencies):**

- **Vue.js 2** - Reactive UI framework
- **OpenAI Chat Completions API** - Streaming API with function calling/tools
- **interact.js** - Drag and resize interactions
- **DOMPurify** - HTML sanitization for security
- **localForage** - IndexedDB persistence wrapper

**Total file size:** ~45KB (one HTML file)

## Getting Started

### Prerequisites

- Modern web browser (Chrome, Firefox, Safari, Edge)
- OpenAI API key ([get one here](https://platform.openai.com/api-keys))

### Installation

**There is no installation.** This is what makes it special.

1. **Download** the `index.html` file
2. **Double-click** to open it in your browser
3. **That's it.** You're done.

No `npm install`, no `pip install`, no `docker pull`, no build scripts. Just a single HTML file that runs instantly.

**Optional: Use a local server for better CORS handling**
```bash
# Using Python
python -m http.server 8000

# Using Node.js (http-server)
npx http-server

# Using PHP
php -S localhost:8000

# Then navigate to http://localhost:8000
```

### First Run

1. Open the application in your browser
2. Enter your OpenAI API key in the toolbar (stored locally in browser only)
3. Select your preferred model (default: gpt-4o)
4. Choose rendering mode (Shadow DOM or Iframe)
5. Start chatting to create designs!

## Usage

### Creating Designs

Simply describe what you want to create:

```
Create a modern hero section with a gradient background
```

```
Design a pricing card with three tiers
```

```
Make a navigation bar with logo and menu items
```

### Editing Designs

Reference designs by name to make changes:

```
Change the background color in Hero to blue
```

```
Replace 'Get Started' with 'Try Now' in Card1
```

```
Add a footer section to the Pricing component
```

### Managing the Canvas

- **Drag**: Click and drag design boxes to reposition them
- **Resize**: Drag the edges or corners of design boxes to resize
- **Duplicate**: Click the duplicate icon on any design box toolbar
- **Export**: Download individual designs as HTML files
- **Delete**: Remove designs you no longer need

### Example Prompts

- "Create a login form with email and password fields"
- "Design a dashboard card showing user statistics"
- "Make a testimonial section with customer reviews"
- "List all current designs"
- "Update the button in Hero to be larger and green"

## Architecture

### How It Works

1. **User Input**: You type a natural language request
2. **AI Processing**: OpenAI's API receives your message and the current state
3. **Tool Calling**: The AI decides which tools to use (create, edit, replace, list)
4. **Streaming Response**: HTML is generated and previewed in real-time
5. **Rendering**: Completed designs are sanitized and rendered in isolation
6. **Persistence**: State is automatically saved to your browser's IndexedDB

### Available Tools

The AI assistant has access to four tools:

- **create_design**: Creates a new design box with HTML content
- **edit_design**: Modifies existing designs (full replacement or instruction-based)
- **replace_string**: Performs surgical string replacements in design HTML
- **list_designs**: Returns all current designs for disambiguation

### Rendering Modes

**Shadow DOM (Default)**
- CSS isolation from the rest of the page
- Better performance
- Recommended for most use cases

**Sandboxed Iframe**
- Complete isolation including JavaScript context
- Additional security layer
- Use for untrusted content

## Security

- **API Keys**: Stored only in your browser's localStorage (never sent to any server except OpenAI)
- **HTML Sanitization**: All generated HTML is sanitized with DOMPurify
- **Script Blocking**: No script tags, event handlers, or dangerous elements allowed
- **Sandbox Isolation**: Iframe mode uses strict sandbox attributes

**Warning**: This application stores your API key in browser localStorage. Do not use in production or on shared computers. For development/personal use only.

## Data Persistence

All data is stored locally in your browser:

- **localStorage**: API key, model selection, render mode preference
- **IndexedDB**: Full conversation history, all designs, canvas positions

Clear your browser data to reset the application.

## Browser Compatibility

Tested and working on:
- Chrome/Edge 90+
- Firefox 88+
- Safari 14+

Requires support for:
- ES6 JavaScript
- Shadow DOM (for default rendering mode)
- IndexedDB
- EventSource (Server-Sent Events)

## Development

### Making Changes

The beauty of a single-file application:

1. **Edit** [index.html](index.html) directly in any text editor
2. **Refresh** your browser (Ctrl+R / Cmd+R)
3. **See changes** instantly

No webpack, no vite, no compilation, no transpilation. Just HTML, CSS, and JavaScript working together in perfect harmony.

### Code Structure

Everything is in [index.html](index.html):
- **CSS** (lines 7-215): Styling and design system
- **HTML** (lines 224-350): Vue.js template
- **JavaScript** (lines 352-1226): Application logic

**How is this possible?** By leveraging:
- CDN-loaded libraries (no node_modules)
- Inline styles and scripts (no separate files)
- Browser APIs for storage (no database server)
- Direct API calls to OpenAI (no backend proxy)

See [CLAUDE.md](CLAUDE.md) for detailed architecture documentation.

### Debugging

- Open browser DevTools console for error messages
- Check Network tab for API request/response details
- Tool execution logs appear in console
- State inspection available via Vue DevTools

## Limitations

- **Client-side only**: No server-side processing or storage
- **API costs**: Each conversation uses OpenAI API credits
- **Browser storage limits**: Large numbers of designs may hit IndexedDB quotas
- **No authentication**: Anyone with access to your browser can use your API key
- **Single session**: No multi-device sync or cloud backup

## Roadmap

Potential future enhancements:
- [ ] Multiple AI provider support (Anthropic, Google, etc.)
- [ ] Component library/templates
- [ ] Export full canvas as single HTML file
- [ ] Image generation integration
- [ ] Collaborative editing
- [ ] Version history for designs
- [ ] Custom tool definitions

## Troubleshooting

**API key not working**
- Verify key is correct at https://platform.openai.com/api-keys
- Check you have available credits
- Ensure no browser extensions are blocking requests

**Designs not rendering**
- Try switching between Shadow DOM and Iframe modes
- Check browser console for sanitization warnings
- Some complex CSS may not work in Shadow DOM

**State not persisting**
- Ensure IndexedDB is enabled in browser
- Check you're not in private/incognito mode
- Clear site data and try again if corrupted

**Streaming stopped/frozen**
- Click Stop button and try again
- Check network connection
- Verify API quota not exceeded

## Why a Single HTML File?

**Philosophy:** Web applications don't need to be complex. This project proves that even advanced features like AI integration, streaming APIs, drag-and-drop interfaces, and persistent storage can fit in a single, readable HTML file.

**Benefits:**
- **Maximum portability**: Send via email, USB drive, or any file transfer method
- **No supply chain attacks**: No hidden dependencies in node_modules
- **Easy to audit**: All code is visible and readable in one place
- **Permanent**: No build tools that become obsolete, just standards-based web code
- **Educational**: Perfect for learning how modern web apps actually work

**This is how the web was meant to be used.**

## Contributing

This is a single-file educational project. Feel free to fork and modify for your own use.

**Want to add features?** Just edit the HTML file. That's the point.

## License

MIT License - Feel free to use this project for personal or commercial purposes.

## Acknowledgments

- Built with [Vue.js](https://v2.vuejs.org/)
- Powered by [OpenAI](https://openai.com/)
- Drag/resize by [interact.js](https://interactjs.io/)
- Sanitization by [DOMPurify](https://github.com/cure53/DOMPurify)
- Storage by [localForage](https://localforage.github.io/localForage/)

## Support

For issues or questions, please open an issue on GitHub.

---

**Note**: This application is for development and personal use. Always protect your API keys and never commit them to version control.