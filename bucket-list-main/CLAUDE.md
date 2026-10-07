# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**나의 버킷 리스트 (My Bucket List)** is a lightweight, client-side task management web application. It's a Vanilla JavaScript project with no build process, no backend server, and no external dependencies beyond Tailwind CSS (via CDN).

### Key Characteristics

- **Framework**: Vanilla JavaScript (ES6+) - no React, Vue, or Angular
- **Styling**: Tailwind CSS (CDN) + custom CSS in `css/styles.css`
- **Data Storage**: Browser LocalStorage API (client-side only)
- **Architecture**: Class-based OOP with modular separation
- **Deployment**: Static files - no build step needed

---

## Architecture & Code Structure

### Module Separation

The codebase is split into two main modules for clarity:

1. **`js/storage.js` (Data Layer)**
   - Handles all LocalStorage operations
   - Exposes `BucketStorage` object with methods: `load()`, `save()`, `addItem()`, `updateItem()`, `deleteItem()`, `toggleComplete()`, `getStats()`, `getFilteredList()`
   - Single responsibility: data persistence and retrieval
   - Items are stored with: `{id, title, completed, createdAt, completedAt}`

2. **`js/app.js` (UI Layer)**
   - Main `BucketListApp` class handles all DOM interactions
   - Methods: `init()`, `bindEvents()`, `handleAdd()`, `handleFilter()`, `handleToggle()`, `handleDelete()`, `render()`, `createBucketItemHTML()`
   - Caches DOM elements in `cacheElements()` for performance
   - Calls `BucketStorage` methods to persist changes
   - Re-renders UI by calling `render()` after data changes

### Data Flow

```
User Action → App Event Handler → Storage Method → LocalStorage
    ↑                                                    ↓
    └────────────── Re-render UI ←────── Get Updated Data
```

### Key Design Decisions

- **No Framework**: Reduces file size and complexity. Direct DOM manipulation is straightforward for this simple use case.
- **Class-based UI**: `BucketListApp` class encapsulates all UI logic in a single namespace.
- **Functional Storage**: `BucketStorage` uses object with methods (not a class) for simplicity.
- **HTML in JavaScript**: `createBucketItemHTML()` generates HTML strings. Simple for small projects, but consider template literals/components if UI complexity grows.
- **Timestamp-based IDs**: Uses `Date.now()` for unique IDs (collision risk minimal for this use case).

---

## Development Workflow

### Running the Application

Choose one of these methods:

**Option 1: Direct Browser (Fastest)**
```powershell
# Windows: Open index.html directly
start .\index.html
# Or double-click index.html in File Explorer
```

**Option 2: Live Server (Recommended for Development)**
```powershell
# VS Code: Install "Live Server" extension
# Then right-click index.html → "Open with Live Server"
```

**Option 3: Python HTTP Server**
```powershell
python -m http.server 8000
# Open http://localhost:8000 in browser
```

### Editing & Testing

Since there's no build step:
1. Edit HTML/CSS/JS files
2. Save changes
3. Refresh browser tab to see updates
4. Use browser DevTools (F12) to inspect and debug

### Browser DevTools Tips

- **Console**: Check for JavaScript errors
- **Application → LocalStorage**: View stored bucket list data
- **Network**: Verify no failed requests (all assets should be available)

---

## File Responsibilities

| File | Purpose | Key Functions |
|------|---------|---|
| `index.html` | Page structure and layout | Contains form, filter buttons, list container, modal |
| `css/styles.css` | Animations and mobile responsive design | `.filter-btn` styles, `@keyframes slideIn/fadeIn/scaleIn`, media queries |
| `js/storage.js` | LocalStorage CRUD operations | `BucketStorage` methods for data persistence |
| `js/app.js` | UI logic and event handling | `BucketListApp` class manages all DOM interactions |
| `README.md` | User-facing documentation | Features, setup, usage guide |

---

## Common Tasks

### Add a New Feature

1. **Update HTML** (`index.html`): Add new DOM elements if needed
2. **Update Storage** (`js/storage.js`): Add new storage methods if data structure changes
3. **Update App Logic** (`js/app.js`): Add event handlers and rendering logic
4. **Update Styles** (`css/styles.css`): Add animations or responsive design adjustments
5. **Test**: Open in browser and verify functionality; check LocalStorage in DevTools

### Modify Styling

- **Tailwind classes**: Update `index.html` Tailwind utility classes
- **Custom CSS**: Add to `css/styles.css` for animations, media queries, or overrides
- **Colors**: Defined via Tailwind (Blue #3b82f6, Green #10b981); change in HTML class names

### Add Data Fields to Bucket Items

1. Update the item structure in `js/storage.js` (e.g., `addItem()` method)
2. Update `js/app.js` rendering logic in `createBucketItemHTML()` to display new fields
3. Test LocalStorage persistence in browser DevTools

---

## Important Notes

### LocalStorage Limitations

- **Size**: ~5-10MB per domain (browser dependent)
- **Persistence**: Data persists across sessions but can be cleared by browser cache clear
- **No Sync**: Data is browser/device specific; no cross-device sync
- **Security**: Plain text storage; don't store sensitive data

### XSS Prevention

The app uses `escapeHtml()` method to sanitize user input before rendering. This prevents XSS attacks from malicious bucket list titles. **Do not remove or bypass this.**

### Browser Compatibility

- Modern browsers only (Chrome, Firefox, Safari, Edge latest versions)
- Requires LocalStorage support (all modern browsers have this)
- No IE 11 support

---

## Future Scalability

If this project grows significantly, consider:

- **Package Manager**: Use npm/package.json for Tailwind CSS and other dependencies (instead of CDN)
- **Build Tool**: Add Webpack/Vite if using npm dependencies
- **Component Framework**: Migrate to Vue/React if UI complexity increases significantly
- **Backend**: Add a server/database if multi-device sync is needed
- **Testing**: Add Jest or Vitest for unit tests (currently no test framework)

---

## Quick Reference

| Task | Command |
|------|---------|
| Run app | `python -m http.server 8000` (then open `http://localhost:8000`) or open `index.html` directly |
| Debug | Press F12 in browser, check Console and Application tabs |
| View stored data | DevTools → Application → LocalStorage → site entry |
| Clear all data | DevTools → Application → LocalStorage → right-click and delete, or call `localStorage.clear()` in console |
