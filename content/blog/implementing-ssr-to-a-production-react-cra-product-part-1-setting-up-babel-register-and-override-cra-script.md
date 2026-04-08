---
title: "Implementing SSR to a production React (CRA) product (Part 1: Setting up Babel Register & Override CRA script)"
date: 2019-02-04
excerpt: Implementing SSR to a CRA project
keywords:
  - Server-Side Rendering (SSR)
  - React
  - Upgrading React module
  - Un-ejecting React
  - Combining API server and client side
  - Creating a Universal app
  - Babel Register module
  - React-script replacement
  - React-app-rewired
  - Customize-cra
  - Middleware rendering
  - State management framework
  - MobX
  - Upgrading an existing project
  - Implementing SSR to a production React (CRA) product
  - Creating a middleware to render
  - SSR Enabled State Management Setup
  - Finalizing a SSR project
  - Backend frameworks
  - Laravel
  - Rails
  - React, Koa, Mongo - RKM
  - Custom Webpacks
  - Handling static files in SSR
  - Overriding CRA scripts.
---

<Callout emoji="💡">
  This page's structure has been updated on June 2023
</Callout>

There I was, trying to find out what the heck is SSR on google. I get server-side rendering, that's basically what backend frameworks like Laravel and Rails do, but what do you mean with React? *Isn't React already good enough?*

Turns out I was wrong. So I started studying and researching about it. Guides after guides. Finally, I got a bit of a grasp on what it is.

The company I am working at now has very highly scaled products. Products that are meant to withstand the traffic they are getting every minute.

> So I figured, *"Cool, I get to maintain one of these products and learn how big projects like these were built like. How tough they are and what tools & technologies are behind the driving force"*

I was appointed to the respondent product. This product is basically where our respondents/panel go to check their dashboard and do surveys, so you can expect this app gets very high traffic, daily.

The product was built in React (with Typescript) as the front end, Koa as the API server framework & MongoDB for the database. Sounds good. React, Koa, Mongo... RKM. *I can't make a word out of the 3 combine. I tried. *React has been ejected, so custom Webpacks were built to maintain the source code. At that time when I checked, the product was on React 15.5+.

I was given 2 tasks, which were upgrading it to the latest React module and un-eject it, which means get it to CRA 2.0, and then combining the API server and the client side into one with SSR.

> Ok...

Sounds good enough. So in this article/post, I will be sharing with you how to integrate an existing React SPA project into a Universal app. Let's begin!

There are 4 things that you need to prepare:

- Babel Register module
- A react-script replacement called react-app-rewired & customize-cra.
- A middleware to render the incoming requests
- A state management framework, in this case, we are using MobX for our React project.
- A good understanding of the 3 things I mention above.

Now as we go through this journey of discovery, I assume you know all those things, If not, please gain a good grasp of the things you lack first, before proceeding. But if you are the type that learns fast, sure let's do this.

I will be breaking this guide into 3 parts. Follow me on [Twitter](https://twitter.com/qwerqy_dev "Twitter") to get notified when the next part will come out:

1.  Implementing SSR to a production React (CRA) product (Part 1: Setting up Babel Register & Override CRA script)
2.  [Implementing SSR to a production React (CRA) product (Part 2: Creating a middleware to render & SSR Enabled State Management Setup)](https://aminroslan.com/posts/implementing-ssr-to-a-production-react-cra-product-part-2-setting-up-babel-register-override-cra-scr "Implementing SSR to a production React (CRA) product (Part 2: Creating a middleware to render & SSR Enabled State Management Setup)")
3.  Implementing SSR to a production React (CRA) product (Part 3: Finalizing the project) (Coming Soon)

## Babel Register

In this project we will be using a module from babel called

```
@babel/register
```

You may start to wonder, *"Why Babel? Why not just go ahead and compile our own Webpack with custom config?"*. That my friend is a good idea too. However, CRA comes built in with their own optimized Webpack config, which means, this saves us a lot of time rather than trying to figure own what type of plugins should we go for in building a Webpack. Remember, this is not a new project. This is upgrading an existing project that has been scaled up for more than 2 years.

Create a new file in the root folder of your server directory, call it bootstrap.js or whatever you want to call it. Let's have a look at how it looks like shall we?

```js title="server/bootstrap.js"
const md5File = require("md5-file")
const path = require("path")

// Ignore CSS styles for loading.
const ignoreStyles = require("ignore-styles")
const register = ignoreStyles.default

// Ignore image requests for loading.
const extensions = [".gif", ".jpeg", ".jpg", ".png", ".svg"]

// Override the default style ignorer, also modifying all image requests
register(ignoreStyles.DEFAULT_EXTENSIONS, (mod, filename) => {
  if (!extensions.find((f) => filename.endsWith(f))) {
    // If we find a style
    return ignoreStyles.noOp()
  } else {
    // If we find an image
    const hash = md5File.sync(filename).slice(0, 8)
    const bn = path.basename(filename).replace(/(\.\w{3})$/, `.${hash}$1`)

    mod.exports = `/static/media/${bn}`
  }
})

require("@babel/register")({
  presets: [
    [
      "@babel/preset-env",
      {
        targets: {
          node: "10"
        }
      }
    ],
    [
      "@babel/preset-typescript",
      {
        isTSX: true,
        allExtensions: true
      }
    ],
    "@babel/preset-react"
  ],
  extensions: [".js", ".jsx", ".ts", ".tsx"],
  plugins: [
    ["@babel/plugin-proposal-decorators", { legacy: true }],
    ["@babel/plugin-proposal-class-properties", { loose: true }],
    "syntax-dynamic-import",
    ["dynamic-import-node", { noInterop: true }],
    "react-loadable/babel",
    "@babel/plugin-transform-async-to-generator"
  ]
})
require.extensions[".vcf"] = () => {}

require("@babel/polyfill")
require("./app")
```

Now don't be scared! Don't just copy whatever that is in the file just yet. Let's go through one by one.

### Handling static files

```js title="server/bootstrap.js"
const md5File = require("md5-file")
const path = require("path")

// Ignore CSS styles for loading.
const ignoreStyles = require("ignore-styles")
const register = ignoreStyles.default

// Ignore image requests for loading.
const extensions = [".gif", ".jpeg", ".jpg", ".png", ".svg"]

// Override the default style ignorer, also modifying all image requests
register(ignoreStyles.DEFAULT_EXTENSIONS, (mod, filename) => {
  if (!extensions.find((f) => filename.endsWith(f))) {
    // If we find a style
    return ignoreStyles.noOp()
  } else {
    // If we find an image
    const hash = md5File.sync(filename).slice(0, 8)
    const bn = path.basename(filename).replace(/(\.\w{3})$/, `.${hash}$1`)

    mod.exports = `/static/media/${bn}`
  }
})
// .....
```

This section of the file is to handle the image parsing to be returned as something readable once it's rendered on the static page before hydration. Now I am not the best when it comes to explaining what's happening here, but basically, without these lines of codes, your images won't show properly on the static page render server side before the client hydrates the page. You can do your own personal touchup on the configs here, feel free to experiment around.

```js title="server/bootstrap.js"
//....
require("@babel/register")({
  presets: [
    [
      "@babel/preset-env",
      {
        targets: {
          node: "10"
        }
      }
    ],
    [
      "@babel/preset-typescript",
      {
        isTSX: true,
        allExtensions: true
      }
    ],
    "@babel/preset-react"
  ],
  extensions: [".js", ".jsx", ".ts", ".tsx"],
  plugins: [
    ["@babel/plugin-proposal-decorators", { legacy: true }],
    ["@babel/plugin-proposal-class-properties", { loose: true }],
    "syntax-dynamic-import",
    ["dynamic-import-node", { noInterop: true }],
    "react-loadable/babel",
    "@babel/plugin-transform-async-to-generator"
  ]
})
require.extensions[".vcf"] = () => {}

require("@babel/polyfill")
require("./app")
```

This side of the script is where the heart of SSR lives. This is basically where you'll need to spend extra time in tweaking. I have 3 presets set which are

- @babel/preset-env - I set a custom option targets to node 10. This enables the ES6+ syntaxes we're using the project. We tested going from 11 to 6, but for our project, the sweet spot was 10. [Source](https://babeljs.io/docs/en/babel-preset-env "babel preset env")
- @babel/preset-typescript - The project uses Typescript, and the component files are .tsx, so I enabled isTSX & allExtensions. [Source](https://babeljs.io/docs/en/babel-preset-typescript "babel preset typescript")
- @babel/preset-react - Pretty obvious in this situation, no custom options for this one. You need this preset for a React app. [Source](https://babeljs.io/docs/en/babel-preset-react "babel preset react")

I set extensions to an array of the file formats that exist in the app. Then for plugins, let's go through it.

- @babel/plugin-proposal-decorators - In this project, we use decorators provided by MobX. I also have to include legacy mode for certain decorators to work. [Source](https://www.npmjs.com/package/@babel/plugin-proposal-decorators "plugin proposal decorators")
- @babel/plugin-proposal-class-properties - Set loose to true. [Source](https://www.npmjs.com/package/@babel/plugin-proposal-class-properties "plugin proposal class properties")
- syntax-dynamic-import - [Source](https://babeljs.io/docs/en/babel-plugin-syntax-dynamic-import "plugin syntax dynamic import")
- dynamic-import-node - Set noInterop to true. [Source](https://www.npmjs.com/package/babel-plugin-dynamic-import-node "dynamic import node")
- react-loadable/babel - If you are planning to code-split, have this as well, in our case, this wasn't needed as we didn't need code-split. [Source](http://github%20-%20jamiebuilds/react-loadable:%20A%20higher%20order%20component%20for%20...%20https://github.com/jamiebuilds/react-loadable "react loadable")
- @babel/plugin-transform-async-to-generator - [Source](http://babel/plugin-transform-async-to-generator%20%C2%B7%20Babel%20https://babeljs.io/docs/en/babel-plugin-transform-async-to-generator "transform async to generator")

We included the .vcf extension for some files in our project, it's fine to not include this.

In our instance, we need [@babel/polyfill](https://babeljs.io/docs/en/babel-polyfill "babel polyfill") for half of the functions included in this project. Lastly, you will need to require your server file where you set all your endpoints, authentications, etc. Basically, it's the file that starts your backend.

Our app is not ready yet to start. So let's save the time tweaking the settings and continue onto the next step.

## Override CRA scripts

#### Continue doing this if you use decorators in your project. Since I use Mobx in this project, we require to have decorators enabled. CRA 2.0 doesn't come with that yet. 

Alright, in this step, it's quite straightforward, this is where we procure a module that enables us to override the CRA built-in Webpack config. CRA 2.0 is a beast! With built-in Typescript & Webpack, but there are things we need to adjust in order to make the SSR process go smoothly. Before going forward, I assume you understand what are the modules we'll be needing.

- react-app-rewired - [Source](https://github.com/timarney/react-app-rewired "react app rewired")
- customize-cra - We need this second file because react-app-rewired alone does not support CRA 2.0 (As of that moment). However, it still depends on react-app-rewired. [Source](https://github.com/arackaf/customize-cra "customize cra")

Create a file in your CRA src file and call it config-overrides.js and take a look at how I did in mine.

```js title="src/config-overrides.js"
const {
  override,
  addBabelPlugin,
  addDecoratorsLegacy,
  disableEsLint
} = require("customize-cra")

module.exports = override(
  addBabelPlugin("babel-plugin-styled-components"),
  addDecoratorsLegacy(),
  disableEsLint()
)
```

As you can see, I pulled out some modules from customize-cra then create a custom module to be used when we init the scripts, will get to that later.

- addBabelPlugin("babel-plugin-styled-components") - To add support on styled components when we init script.
- addDecoratorLegacy() - Need this to enable decorators legacy.
- disableEslint() - Disables CRA 2.0 built-in eslint so that it won't complain about the *unorthodox *method we are going to do.

Next, let's customize your scripts in package.json to use the modified script.

```json title="package.json"
"scripts": {
    "build": "react-app-rewired build", // this file here
    "build:android": "...",
    "server:android": "...",
    "server:dev": "...",
    "start:dev": "...",
    "start:dev:android": "...",
    "start": "node server/bootstrap.js"
  },
```

Ignore the empty scripts I did that on purpose. On the build script it was initially react-script build, change the "react-script" to "react-app-rewired".

And that's about it from my side. Have a look at their guides on how you can customize more.

Once you have set up these 2 things, you are prepared to face what's coming. Do remember, if anything happens when you are trying to start the project, refer to these 2 files because these 2 files are involved heavily in transpiling your whole project. But let's not worry about that now.

I will continue in the next post. On setting up the middleware and server-side state management. I will post on [Twitter](https://twitter.com/qwerqy_dev "twitter") when the next part of this guide to be out. Until then, this is Part 1. See you on the next one!

I will be breaking this guide into 3 parts. Follow me on [Twitter](https://twitter.com/qwerqy_dev "twitter") to get notified when the next part will come out:

1.  Implementing SSR to a production React (CRA) product (Part 1: Setting up Babel Register & Override CRA script)
2.  [Implementing SSR to a production React (CRA) product (Part 2: Creating a middleware to render & SSR Enabled State Management Setup)](https://aminroslan.com/posts/implementing-ssr-to-a-production-react-cra-product-part-2-setting-up-babel-register-override-cra-scr "Implementing SSR to a production React (CRA) product (Part 2: Creating a middleware to render & SSR Enabled State Management Setup)")
3.  Implementing SSR to a production React (CRA) product (Part 3: Finalizing the project) (Coming Soon!)
