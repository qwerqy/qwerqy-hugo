---
title: How SSR Works in React
date: 2025-02-20
tags:
  - react
comment: No way...
---

Server-Side Rendering (SSR) is when React components are rendered to HTML on the server before being sent to the client. Here's the flow:

1. **Server Receives Request**

   - User requests a page
   - Server runs React code

2. **Server Renders React**

   - React components are rendered to HTML strings
   - Initial state is serialized

   ```jsx
   // Example server code
   const html = ReactDOMServer.renderToString(<App />)
   const state = JSON.stringify(initialState)
   ```

3. **Server Sends Response**

   - HTML is sent to browser
   - Initial state is included in a script tag
   - React client bundle is included

4. **Client Hydration**
   - Browser loads React
   - React "hydrates" the HTML by attaching event listeners
   - App becomes interactive without re-rendering

Benefits:

- Faster initial page load
- Better SEO (search engines see complete HTML)
- Better performance on slow devices

This is called "isomorphic" or "universal" rendering because the same React code runs on both server and client.
