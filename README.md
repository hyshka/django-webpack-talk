# django-webpack-demo
Tech demo for serving your SPA as a Django App while maintaining the development tools from Webpack's DevServer

## With the help of...
- https://github.com/owais/django-webpack-loader
- https://github.com/owais/webpack-bundle-tracker
- https://webpack.js.org/
- https://facebook.github.io/create-react-app/
- https://docs.djangoproject.com/en/2.1/ref/django-admin/#startapp

# Installation

## settings.py
```
WEBPACK_LOADER = {
    "DEFAULT": {
        "BUNDLE_DIR_NAME": "bundles/",
        "STATS_FILE": str(BASE_DIR / "techmeet/apps/frontend/webpack-stats.json"),
    }
}
```

## urls.py
```
path("app/", include("frontend.urls")),
```
