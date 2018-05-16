# Routers

To manipulate the URL, views have Routers. Webix Jet has four predefined types of routers.

| Type | Displays app URL | Stored |
| --- | --- | --- |
| HashRouter | [https://myshop.com/\#!/my/app](https://myshop.com/#!/my/app) | Yes |
| UrlRouter | [https://myshop.com/my/app](https://myshop.com/my/app) | Yes |
| StoreRouter | No | Yes |
| EmptyRouter | No | No |

## 1. Hash Router \(default\)

The app URL is displayed after a hashbang. As this router is set by default, there's no need to to define it in the config.

```javascript
// myapp.js
import {JetApp, HashRouter} from "webix-jet";

var app = new JetApp({
    start: "/demo/details",
    router: HashRouter //optional
}).render();
```

### Hiding the "!" in the URL

You can hide the "!" in the app URL by using the **routerPrefix** parameter in the app config. Have a look:

```javascript
// myapp.js
import {JetApp, HashRouter} from "webix-jet";

var app = new JetApp({
    start: "/demo/details",
    router: HashRouter, //optional
    routerPrefix:""
}).render();
```

If you set **routerPrefix** to an empty string, the app URL will look like this:

```text
https://myshop.com/#/sales/top
```

## 2. URL Router

If you choose UrlRouter, the app URL is displayed without a hashbang. There is a trick with this router: your server-side code should be compatible. You need to provide redirects to avoid error 404.

You must choose _UrlRouter_ in the app configuration:

```javascript
// myapp.js
import {JetApp, UrlRouter} from "webix-jet";

webix.ready(() => {
    const app = new JetApp({
        router: UrlRouter,
        start: "/top/start"
    });
    app.render();
});
```

Next, configure http redirects by adding a fallback. For webpack dev server from a starter kit, this can be done in _webpack.config_:

```javascript
// webpack.config.js
devServer:{
    historyApiFallback:{
        index : "index.html"
    }
}
```

In production apps, this can be done through _apache/nginx_ configuration.

If the app is hosted in a folder, you must provide a router prefix:

```javascript
// myapp.js
import {JetApp, UrlRouter} from "webix-jet";

webix.ready(() => {
    const app = new JetApp({
        router: UrlRouter,
        routerPrefix: "/myapp", //!
        start: "/top/start"
    });
    app.render();
});
```

In your _index.html_ you should set the relative URL with the same prefix:

```markup
<!-- index.html -->
<script type="text/javascript">
    if(document.location.pathname == "/index.html")
        document.location.href = "/myapp";
</script>
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/routers-url.js)

## 3. Store Router

With this guy, the app URL isn't displayed at all, but it is stored in the session storage. So no worries, you can still return to the previous and next views as if they are in the URL. This can be useful if you have a multilevel application \(apps are subviews of other apps\). The Store router is set for the enclosed apps because the address bar is already taken by the outer app. Suppose you have closed an app module with a deep level of subviews and expect to be in the same place of this app when you switch to it again. The Store router allows this.

Here's an app module with a form view:

```javascript
// app1.js
import {JetApp, StoreRouter} from "webix-jet";

var app1 = new JetApp({
    start: "/form",
    router: StoreRouter
});
```

Next, the app module is included into a view and the view is included into another app:

```javascript
// views/page.js
const PageView = () => ({
    rows: [app1]
});

// app2.js
import {JetApp, HashRouter} from "webix-jet";

var app2 = new JetApp({
    start: "/page",
    router: HashRouter
}).render();
```

## 4. Empty Router

If you don't want to store the app part of the URL, there's the EmptyRouter for you. The app URL isn't displayed and isn't stored. It's also used for nested apps.

```javascript
// app1.js
import {JetApp, EmptyRouter} from "webix-jet";

var app1 = new JetApp({
    start: "/form",
    router: EmptyRouter
});
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/b686944b383745070fc977aa9123f01a36ce2b3c/sources/viewapp.js)

## 5. Custom Routers

If these four routers aren't what you want, you can define your own.

A router must be a class with the following methods:

* **constructor**\(callback, config\), where _callback_ is a function called to set the correct URL and _config_ can contain the router prefix;
* **set**\(path, config\), where _path_ is the app URL and _config_ can contain some additional options for setting the URL;
* **get**\(\) that returns the URL.

