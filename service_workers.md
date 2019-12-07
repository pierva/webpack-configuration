# Service Workers

Service workers allow you to push notifications from a webapp, and in general they help creating webapp that run in the browsers but they act like proper applications.
Service workers allow also offline access. Basically a service worker creates a cached version of the website that they can supply if the server can't be reached.

Add service worker only on the production configuration.

Google Workbox makes working with basic Service Workers very easy.

**Install the plugin**
```sh
$ npm install workbox-webpack-plugin --save-dev
```

**Require the plugin in production**
```js
const WorkboxPlugin = require('workbox-webpack-plugin');
```

**Add the plugin**
```js
new WorkboxPlugin.GenerateSW()
```

## Options
There are a lot of options you can pass to the service worker plugin. THings like cache images at runtime, limit the max size of the cache, etc.

## Register a service worker
After installing and configuring the plugin, we need to register the service worker on our html file to make use of it.

At the bottom of the html page, add the following code.

```html
<script>
    // Check that service workers are supported
    if ('serviceWorker' in navigator) {
        // Use the window load event to keep the page load performant
        window.addEventListener('load', () => {
            navigator.serviceWorker.register('/service-worker.js');
        });
    }
</script>
```

Now that you have your service worker, you can run the build command for production, start the server (i.e. express), without using the webpack-dev-server, navigate to the backend server port and route that serves the  `dist` folder, once the page is loaded, shut down the server. Now you can reload the page in the browser and you'll see that the page still works.