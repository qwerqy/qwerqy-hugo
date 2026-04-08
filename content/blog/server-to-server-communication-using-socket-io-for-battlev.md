---
title: "Server to server communication using Socket.io for Battlev"
date: 2019-04-19
excerpt: Implementing Socket.io
keywords:
  - Project Battlev
  - Interface design
  - Color scheme
  - API server to Typescript
  - Type checking
  - API server and Discord client server communication
  - Sockets
  - Dashboard setup
  - Clientbot's nickname
  - PUT request
  - Endpoint
  - Document update
  - Discord.js documentation
  - EventListener
  - Sockets.io
  - Real-time communication
  - Bidirectional communication
  - Socket.io server
  - Socket.io client
  - Emitters and listeners
  - Express' built-in app.set() and app.get()
  - Server-to-server communication.
---

_Progress Update: Project Battlev is progressing just nicely. Already decided on what type of design I want for the interface, the color scheme. I've also converted my API server to Typescript for type checking. In this post I will try my best to share how I implemented my idea on how to make my API server and my Discord client server to communicate with each other using Sockets._

## Back to back communication.

So to bring you guys up to speed, I've set up the dashboard, and have placed in the ability to change my Clientbot's nickname in one of my guilds in Discord. I do this by doing a PUT request to my API server and within the endpoint, it will update the document based on the body of the request. All good and dandy, sounds like it's done. But not quite...

The next bit, I must find a way to make the update that I made to be listened by the Clientbot. Somewhat like, making the bot to make a GET request to the API server to get the latest updated document from the database and run the update back with the Discord API, updating the Clientbot's nickname in the end. Cool, so I started going through the official [Discord.js](https://discord.js.org/#/ "discord.js") documentation to search for a specific eventListener that has the ability to listen for the change. Well, there's none.

I started looking for alternatives, and then I stumbled upon [Sockets.io](https://socket.io/ "sockets.io"). So based on what it's stated in the website,

> _Socket.IO enables real-time, bidirectional and event-based communication. \*\*It works on every platform, browser or device, focusing equally on reliability and speed. _

Sounds like a good fit in my use case. So I gave it a try.

I set up the socket.io server on my API server, and the socket.io client on the Clientbot's server, set up a few emitters and listeners and everything works like expected! I implemented Express' built in app.set() and app.get() to store the socket function and use it in another location. More info [here](https://expressjs.com/en/api.html#app.set "app.set()"). So this is how it is implemented.

![undefined](https://cdn.buttercms.com/LtYsvUjAS4pahYgJvRPg)

That's on my API server, and how it was implemented in the Client bot is as follows:

![undefined](https://cdn.buttercms.com/kDagYABXRxuZta9rNnou)

With that, I managed to make the servers talk to each other. This is a really early implementation and I wouldn't say its production ready yet but it's a good starting point to work your way up. I will continue to update more about this in the future.

With that said, thanks for taking your time reading!
