---
title: "Implementing SSR to a production React (CRA) product (Part 2: Middleware & State Management)"
date: 2019-02-20
excerpt: How I added middleware and state management
keywords:
  - Server Side Rendering
  - SSR
  - React
  - Middleware
  - Koa
  - Mobx
  - State Management
  - CRA Configs
  - Webpack
  - Bootstrap File
  - React App Rewired
  - StaticRouter
  - React Router Dom
  - RenderToString
  - ReactDOM Server
  - Node.js
  - Production React App
  - renderer.js
  - Babel Register
  - Koa-router
  - ReactDOM.hydrate
  - serverRenderer
  - HTML Generation
  - npm Run Build
  - package.json
  - SSR App
  - getInitialProps()
  - Provider Component
  - window.INITIAL_STATE
  - app.store.tsx
  - Plugins for SSR.
---

<Callout emoji="💡">
  This page's structure has been updated on June 2023
</Callout>

Hello guys, welcome back! This is part 2 of the guide series on how to implement SSR to your ongoing production React product.

## Recap

So in part 1 of the guide series, I showed you how I prepared the bootstrap file that will be the entry script to run our project, and by using react-app-rewired how we can override the current CRA configs to enable some Webpack configs to work with SSR.

I will be breaking this guide into 3 parts. Follow me on [Twitter](https://twitter.com/qwerqy_dev "Twitter") to get notified when the next part will come out:

1.  [Implementing SSR to a production React (CRA) product (Part 1: Setting up Babel Register & Override CRA script)](https://aminroslan.com/posts/ssr-to-an-ongoing-production-react-product-part-1 "Implementing SSR to a production React (CRA) product (Part 1: Setting up Babel Register & Override CRA script)")
2.  Implementing SSR to a production React (CRA) product (Part 2: Creating a middleware to render & SSR Enabled State Management Setup)
3.  Implementing SSR to a production React (CRA) product (Part 3: Finalizing the project) (Coming Soon)

In this part, I will be sharing with you 2 things:

1.  Creating a middleware that will start the process of SSR.
2.  State Management Setup, in this guide, we'll be using [Mobx](https://mobx.js.org/ "Mobx") as our state management framework.

## Renderer Middleware

Let's start off with creating a middleware that will be responsible for rendering all of our client-side code on the server side. Create a file called `renderer.js` and place it in your `middleware` folder.

Let's create an empty module to start off with, since the project uses Koa for its backend, we'll mostly be using ctx.

```js title="middleware/renderer.js"

// import our main Routes component. The main file for the project is routes.tsx

const path = require("path")
const fs = require("fs").promises

// This is the module that will be responsible for most of the rendering.
export default async (ctx) => {
  const filePath = path.resolve("build", "index.html")
  let html = await fs.readFile(filePath, "utf8")

  let context = {}
  let component

  try {
    component = renderToString(
      <StaticRouter location={ctx.request.url} context={context}>
        <Routes />
      </StaticRouter>
    )
  } catch (err) {
    // If something goes wrong with the component markup, this will light up.
    console.log("Component Error: ", err.stack)
  }
  ctx.body = html
    .replace(
      "</head>",
      `<script>window.__INITIAL_STATE__ = ${dehydratedStore}; </script></head>`
    )
    .replace('<div id="root"></div>', `<div id="root">${component}</div>`)
}
```

Okay, after cutting down most of the codes in this file, I manage to get the actual starting point from where I started when I was working on this project. We have a typical reusable middleware, let's go one by one on the stuff:

We use Node's built-in modules, fs and path (In this case, I am using an experimental module from fs which is async),  and get the HTML that is generated when we run the build script.

```js
const filePath = path.resolve("build", "index.html")
let html = await fs.readFile(filePath, "utf8")
```

Resolve the path to `build/index.html` . If you don't see an index.html file in the build folder, run` npm run build `to generate it.

Secondly, we import StaticRendering from react-router-dom and renderToString from react-dom/server to be used in our HTML we create a component variable that consists of these following modules. We create it as such:

```js
const filePath = path.resolve("build", "index.html")
let html = await fs.readFile(filePath, "utf8")

let context = {}
let component

try {
  component = renderToString(
    <StaticRouter location={ctx.request.url} context={context}>
      <Routes />
    </StaticRouter>
  )
} catch (err) {
  // If something goes wrong with the component markup, this will light up.
  console.log("Component Error: ", err.stack)
}
```

Based on the documentation from React Router, to enable rendering in server side, we use StaticRouter to render our routed components in the client-side to static lines of codes, this enables the project to render any pages inside of our Router component to static when we go from page to page. There are two props needed, location and context. put in `ctx.request.url `or `ctx.url` into the location prop, and context as an empty object. This will be populated later when we actually use the renderer. Nested within the StaticRouter component will be the root file of our client-side app, in this case, is the Routes component. In your case, it can be App.js or usually, it's Index.js in most boilerplate CRA 2.0 app.

I place the logic inside of a `try...catch` function so that I can catch any errors related to SSR. This is really useful when you're in development. Once you have stabilized the renderer file, you can either remove it or just leave it as is in case you want to develop more stuff in it.

- Next, we'll send back a response body with the component and the HTML file.

```js
ctx.body = html.replace(
  '<div id="root"></div>',
  `<div id="root">${component}</div>`
)
```

I use the `.replace` method which will add the component to the location I want. In this case, it's the div element with id `root`.

Next, we'll go and add the middleware function into our controllers. I like to keep things neat, so in this project, I created separate folders for all the controllers needed in the server. Let's create the Index Controller and use it in our main server file.

```js title="controllers/index.js"

const router = new Router()

router.get("/", serverRenderer)

export default router
```

Then in our server file.

```js title="server.js"

const serve = require("koa-static")

const koa = new Koa()
const router = new Router()

//....

// Root static page.
koa.use(indexController.routes())

//...

// Public Controllers
koa
  //... your other controllers
  // This is the static middleware.
  .use(
    serve("build", {
      hidden: true
    })
  )

//...

// Renders other routes for SSR
koa.use(serverRenderer)

//...

// This is where your server listens to port..
```

Make sure you are following this order when you are customizing your server file. Since Koa goes from top to bottom, it's important to have them in the correct position to reduce the errors that'll show later on as we progress.

Next, let's head over to the client-side and modify our entry file, in this project, it's in ` src/``index.tsx `.

```tsx title="src/index.tsx"
// src/index.tsx

//...declare imports

//...

ReactDOM.hydrate(
  <Router history={history}>
    <Routes />
  </Router>,
  root
)
```

Nothing much to change here, just change your `ReactDOM.render`to`ReactDOM.hydrate`. This enables the client-side to take over the server-rendered static page once all of our JS files are loaded and set.

Lastly, before you start running your app, go to your` package.json` and add/modify the script you have that starts the server

```json title="package.json"
"scripts": {
  "build": "react-app-rewired build",
  "start": "node server/bootstrap.js"  //depending on where you put your bootstrap file, in this case i have it in my server folder.
}
```

That is basically how I've set up the project that enables SSR. However, once you start your project, you are bound to get errors, so you can start debugging from there on, but believe me, once you've done debugging, you will understand more on how it all actually works.

Now, run `npm run build && npm run start` and see how it goes! If all goes well, you should start seeing your server listening to a port. Go to localhost:`<PORT>` and check your SSR app out!

If all works, great job! You've successfully integrated SSR into your project. However, it is not the end for me, since this project is quite big, it has to have a state management framework that runs above all, assuming you already have Mobx (or any other state management framework setup on the client-side), in the next step, I won't be showing you how to set it up from scratch, but on just how to enable it to work on the server-side.

## State Management on the Server-Side

In this project, we are using Mobx as the state management framework. If you are not using Mobx as the state management framework, don't worry as we won't get into much detail on it. Regardless, they all bear the same logic, to have a global state stored somewhere in the HOC.

In Next.js, they have something called getInitialProps() where you can initialise the needed props before the client-side components hydrate in. This can include things like fetch, declaring new props and so on. In the next step, I will be doing the same thing, but better and modular.

In your renderer.js file, declare your Store and link it with a Provider component.

```js
export default async (ctx) => {
  // Initialize mobx store. Store will refresh everytime you jump
  // onto a new page or refreshing a page.
  const store = new AppStore()
  // Makes mobx work with SSR
  useStaticRendering(true)
  const filePath = path.resolve("build", "index.html")
  let html = await fs.readFile(filePath, "utf8")

  // ....

  // Dehydrate MobX store to be passed into client.
  const dehydratedStore = stringify(toJS(store))
  let context = {}
  let component

  try {
    component = renderToString(
      <Provider store={store}>
        <StaticRouter location={ctx.request.url} context={context}>
          <Routes />
        </StaticRouter>
      </Provider>
    )
  } catch (err) {
    // If something goes wrong with the component markup, this will light up.
    console.log("Component Error: ", err.stack)
  }
  ctx.body = html
    .replace(
      "</head>",
      `<script>window.__INITIAL_STATE__ = ${dehydratedStore}; </script></head>`
    )
    .replace('<div id="root"></div>', `<div id="root">${component}</div>`)
}
```

Let's go one by one:

- I created a store variable.
- I set useStaticRendering to true. This is exclusive to Mobx, please refer on how to do something similar with your prefered state management framework.
- I wrap the component with a Provider component with one prop which is the store.
- On the HTML, right before the `</head>`tag, i place a script containing  `window.__INITIAL_STATE__`that contains the dehydrated store we just created. This will be the transport of our server rendered props & states to our client-side to hydrate. That being said, let's look at the client-side.

```js
//...

const dehydratedStore = window.__INITIAL_STATE__;
// Rehydrate store
const root = document.getElementById("root") as HTMLElement;
const store = root.hasChildNodes()
  ? new AppStore(dehydratedStore)
  : new AppStore();

//...

ReactDOM.hydrate(
  <Provider store={store}>
    <Router history={history}>
      <Routes />
    </Router>
  </Provider>,
  root
);
```

I grabbed the `window.__INITIAL_STATE__`and place it in a variable called dehydratedStore. Then I replace the current AppStore state with the dehydratedStore. Moving on, we wrap our component with a Provider component. It is important to make sure the components are in the same order as the components in renderer.js.

```tsx title="src/index.tsx"
ReactDOM.hydrate(
  <Provider store={store}>
    <Router history={history}>
      <Routes />
    </Router>
  </Provider>,
  root
)

// in middleware/renderer.js

component = renderToString(
  <Provider store={store}>
    <StaticRouter location={ctx.request.url} context={context}>
      <Routes />
    </StaticRouter>
  </Provider>
)
```

Lastly, in order for the Store to initialize the props properly, we need to add a constructor in the Store file. In this case, our project keeps all global state in app.store.tsx

```js
class AppStore {
  // ....

  constructor(initialState?) {
    if (initialState) {
      Object.assign(this, initialState)
    }
  }

  // .....
}
```

Let's add our little getInitialProps() logic into the mix. From here on, you should start on doing things based on what you want to be rendered on the server-side, in our case, we want information to be fetched even before the static page is rendered. This is so that we can eliminate a certain *loading screen. *So I added a section in the renderer.js for plugins.

### Plugins

"Plugins" is really something I come up with out of the blue. It is where you can call your own custom functions to be executed on server-side for the client-side.

However, I will have to stop here. I will continue on how to write and create custom plugins that enable us to run functions on the server-side that will populate the store for our client-side, and perhaps eliminating the *loading screen... . *

Thank you for taking the time to read the guide. This the end of Part 2. I see you in Part 3!

I will be breaking this guide into 3 parts. Follow me on [Twitter](https://twitter.com/qwerqy_dev "Twitter") to get notified when the next part will come out:

1.  [Implementing SSR to a production React (CRA) product (Part 1: Setting up Babel Register & Override CRA script)](https://aminroslan.com/posts/ssr-to-an-ongoing-production-react-product-part-1 "Implementing SSR to a production React (CRA) product (Part 1: Setting up Babel Register & Override CRA script)")
2.  Implementing SSR to a production React (CRA) product (Part 2: Creating a middleware to render & SSR Enabled State Management Setup)
3.  Implementing SSR to a production React (CRA) product (Part 3: Finalizing the project) (Coming Soon)
