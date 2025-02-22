# manualHistory
> An HTMX Extension for Manual History Restoration

## Overview
manualHistory is a lightweight HTMX extension that exposes a htmx.restoreHistory(path) function. Instead of re‑implementing HTMX’s internal history logic, this extension leverages the native History API and HTMX’s own popstate handler to trigger history restoration using the cached page (or a server reload if needed).

## Why?
When you build pages with infinite scroll or incremental requests, the URL alone may not represent the entire page state. This extension lets you create a back button that “restores” the page state via HTMX’s history cache.

## Installation
1. Include HTMX on your page.
2. Include this extension script (or add it as a module in your project):
  ```html
  htmx.defineExtension("manualHistory", {
    init: function(api) {
      htmx.restoreHistory = function(path) {
        // Push the target URL into history so location updates.
        history.pushState({ htmx: true }, '', path);
        // Dispatch a popstate event—HTMX's onpopstate handler will pick this up.
        window.dispatchEvent(new PopStateEvent('popstate', { state: { htmx: true } }));
      };
    }
  });
  ```
3. Initialize HTMX normally and load the extension (if you’re bundling, ensure it runs after HTMX is loaded).

## Usage

Simply call the exposed function when needed. For example, to add a “Back” button:

```html
<button onclick="htmx.restoreHistory('/page-to-restore')">
  Back
</button>
```

This triggers HTMX’s built-in restoration logic by pushing the URL into history and dispatching a popstate event. No re‑implementation of core logic—just native history API plus HTMX’s cache.

## Notes
- This extension leverages the existing htmx-history-cache. If the cached page isn’t available, HTMX will fall back to a server request.
- Ideal for pages that are built up with incremental content (e.g., infinite scroll).
