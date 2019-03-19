# django-webpack-demo
Tech demo for serving your SPA as a Django App while maintaining the development tools from Webpack's DevServer

## With the help of...
- https://github.com/owais/django-webpack-loader
- https://github.com/owais/webpack-bundle-tracker
- https://webpack.js.org/
- https://facebook.github.io/create-react-app/
- https://docs.djangoproject.com/en/2.1/ref/django-admin/#startapp

# Talk
View the `SLIDES.md` for my notes on installation and why I wanted to explore this implentation.

# Installation

## Django
```
pip install django-webpack-loader
```
### settings.py
```
INSTALLED_APPS = [
    "webpack_loader",
    "frontend",
]

WEBPACK_LOADER = {
    "DEFAULT": {
        "BUNDLE_DIR_NAME": "bundles/",
        "STATS_FILE": os.path.join(BASE_DIR, "djangoproject/apps/frontend/webpack-stats.json"),
    }
}
```
### urls.py
```
path("app/", include("frontend.urls")),
```
## JavaScript
```
npm install webpack-bundle-tracker
```
## webpack.config.js
```
var BundleTracker = require("webpack-bundle-tracker");

module.exports = {
  entry: "./src/index",
  output: {
    path: path.resolve(__dirname, "static/frontend/bundles"),
    publicPath: "http://localhost:3000/"
  },
  devServer: {
    publicPath: "http://localhost:3000/",
    hot: true,
    port: "3000",
    host: "0.0.0.0",
    headers: {
      "Access-Control-Allow-Origin": "*"
    },
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new BundleTracker({ filename: "webpack-stats.json" })
  ],
  ...
}
```
