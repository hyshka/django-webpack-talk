%title: Developing your SPA with Django and Webpack
%author: Bryan Hyshka
%date: 2019-04-04

# Developing your SPA with Django and Webpack

By Bryan Hyshka

---

## Overview

- Explore two common architectures for integrating a Single Page App with Django

- Live demo of the techmeet app (Same Origin)

- Explain how Django serves the Single Page App in production

- Explain how we use Webpack's DevServer in development

- Summarize why I'm interested in exploring the Same Origin model

---

## Archicture #1: Distinct Origins

                        +-------------------+
                        |    Web Browser    |
                        +----/--------\-----+
                            /          \
                           /            \
           +--------------/----+    +----\--------------+
           |  Frontend         |    |  Backend          |
           |  app.example.com  |    |  api.example.com  |
           +-------------------+    +-------------------+

---

## Archicture #1: Distinct Origins

- Frontend + Backend codebases are stored in separate repos and live on
  different domains

- Frontend serves HTML and static files

- Requires that you set up CORS (cross-origin resource sharing) policies on your
  Backend

- JWT (JSON Web Token) authentication is most common

- No need to worry about CSRF (cross-site request forgery) because JWTs don't
  provide ambient authority

---

## Archicture #2: Same Origin

                        +-------------------+
                        |    Web Browser    |
                        +---------|---------+
                                  |
                                  |
                        +---------|---------+
                        |  Backend          |
                        |  app.example.com  |
                        +---------|---------+
                                  |
                                  |
                        +---------|---------+
                        |  Frontend         |
                        +-------------------+

---

## Archicture #2: Same Origin

- Frontend + Backend codebases stored in the same repo and live on the same
  domain

- Backend serves HTML and static files for the Frontend

- No need to set up CORS policies since API calls to the Backend are made to the
  same domain

- Cookie based session authentication is most common (this is the default in
  Django)

- Since cookies provide ambient authority you will need to do some extra work to
  prevent CSRF attacks

  - In Django, sometimes the first page load doesn't generate a CSRF token, or
    it is outdated such as when logging in/out doesn't trigger a full page
    reload or logging in/out in another window

  - To workaround this, read the token from the CSRF cookie before each request
    and if it doesn't exist request a new token from a dedicated endpoint

---

## Live demo of techmeet app

---

## How does Django serve the SPA?

- Webpack on the Frontend outputs bundles to Backend's static directory

- Backend has a route set for `/app/` that will render the Frontend's static
  HTML page

- The browser fetches all assets from the Backend

```
<html lang="en">
  <head>...</head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
    <script src="/static/bundles/main.js"></script>
  </body>
</html>
```

---

## What about local development?

- On the Frontend we run Webpack's DevServer and tell it to proxy the Backend

- The browser fetches all assets from the Frontend's Webpack DevServer

- Bundles in memory will override bundles on the filesystem

- Since the browser is connected to the Webpack DevServer, we can enable HMR
  (hot module replacement)

- If you run it inside a Docker container, the host and container port needs to
  be the same

---

## What about local development?

```
module.exports = {
  entry: "./techmeet/static/app/index",
  output: {
    path: path.resolve(__dirname, "techmeet/static/bundles"),
    filename: "main.js",

    // The url to the output directory resolved relative to the HTML page
    publicPath: "/static/bundles/",
  },
  devServer: {
    // The bundled files will be available in the Browser under this path
    // Webpack recommends this to be the same as output.publicPath
    publicPath: "/static/bundles/",

    // Listen to all network interfaces
    host: "0.0.0.0",
    port: "3000",

    // Forward all requests from the Browser to the Backend
    proxy: {
      "**": "http://localhost:8000",
    },

    // Enable Webpack's Hot Module Replacement
    hot: true,
  },
  plugins: [new webpack.HotModuleReplacementPlugin()],
  ...
}
```

---

## Why Same Origin?

- Easy to integrate into an existing Django project

- If SEO is a concern you can build your public-facing site with static HTML
  pages, or even integrate a CMS if you need it

- Frontend and Backend code maintained in the same repository makes it easy to
  test the application as a whole

- Deploying the application as a single unit makes it easier to synchronize
  changes and track releases

- It can aid collaboration on teams where full-stack developers build the
  application as a whole

---

## References

- https://github.com/keeners/techmeet
- https://fractalideas.com/blog/making-react-and-django-play-well-together/
- https://webpack.js.org/
- https://webpack.js.org/configuration/dev-server/
- https://webpack.js.org/concepts/hot-module-replacement/
- https://en.wikipedia.org/wiki/Cross-origin_resource_sharing
- https://en.wikipedia.org/wiki/JSON_Web_Token
- https://en.wikipedia.org/wiki/Cross-site_request_forgery
