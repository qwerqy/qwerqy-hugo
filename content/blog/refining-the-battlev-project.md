---
title: "Refining the Battlev Project"
date: 2019-03-24
excerpt: Refining my Discord bot project
keywords:
  - Battlev side project
  - Heroku setup
  - Bot for music in voice channels
  - Cleverbot API integration
  - Microservices-based architecture
  - Monolith Architecture
  - Docker container
  - Load balancer application
  - Traefik
  - Project management with Trello
  - Live streaming project on Twitch
  - Blogging about software development
  - Webapp development
  - Bot configuration
  - Server interaction logs.
---

Hello again, in this short blog post I will be sharing about my thoughts on one of my side projects, Battlev.

(If you guys don't know what Battlev is, check out this post [here](https://aminroslan.com/posts/battlev "battlev").)

For the past 1 month, I've setup a semi-production build of Battlev on Heroku alongside it's landing page. There are two components, the page and the bot itself. For the bot, I've placed it in 4 servers where all of them are my friends'. The bot is mostly used for it's ability to play music in voice channels, and having light chats via the Cleverbot API integration. However, it only lasted until last week as my free dyno for this month has depleted. So I'm taking this project a bit further.

For the next iteration, I will extend this project with the knowledge I have by creating a proper webapp for users to interact with the bot's config, and to see the logs of the bot interaction (on the user's server).

![undefined](https://cdn.buttercms.com/X90NvS2wQ1CPQNEtcZUF)

I plan on creating a *Microservices-based architecture within a Monolith Architecture. *I know this is a debatable topic, but it's basically a Monolith Architecture as a whole. I just like calling it microservices of how I define them. By this I mean, I will be having 3 directories in a single project, each with their own node modules and package.json. The project will be deployed in a single Docker container with a load balancer application. (I will be playing around with [Traefik](https://traefik.io/ "traefik").).

There is a private Trello board for this project that I will use to keep track of the project.

I am planning to stream this project live on Twitch as I am doing it ([Channel](https://twitch.tv/greencheese "twitch")). It will mostly be on the weekend. I will also blog about the project bit by bit here on this blog. I hope everything goes well for this project!
