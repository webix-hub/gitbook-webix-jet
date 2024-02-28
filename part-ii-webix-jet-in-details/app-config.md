# App Config

An app module is defined as an extension of the JetApp class (OOP inheritance).

In the app file, you will typically include:

* stylesheets \(any CSS or LESS\)
* custom widgets
* app-level plugins
* app-level Webix settings

You can include various parameters into the app configuration. The details are below.

| Parameter | What For |
| :--- | :--- |
| debug | to enable debugging |
| name | to set the name of the app |
| router | to change a router |
| routes | to shorten the app URL |
| start | to set the start app URL |
| views | to change view modules names |
| arbitrary parameters | e.g. access mode, screen size, etc. |

## Setting the Start URL

The app UI will be rendered from the URL elements from **start** when you open the app for the first time. The app splits the URL into parts, finds the corresponding files in the **views** folder and creates an interface by combining UI modules from those files.

```javascript
// myapp.js
import "./styles/app.css";
import {JetApp, EmptyRouter, HashRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            router: BUILD_AS_MODULE ? EmptyRouter : HashRouter,
            debug: !PRODUCTION,
            start: "/top/layout" // !
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

## Debugging

You really should enable the **debug** mode during the development stage. When _debug_ is enabled, error messages are logged into console and a debugger will pause the app on errors. Note that `webix.debug({ events: true });` also should be switched on.

```javascript
// myapp.js
import "./styles/app.css";
import {JetApp, EmptyRouter, HashRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            router: BUILD_AS_MODULE ? EmptyRouter : HashRouter,
            debug: !PRODUCTION,  // !
            start: "/top/layout"
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

[You can read more about debugging with Webix in this blog post](https://blog.webix.com/ui-development-and-debug-with-webix-js/).

## Choosing a Router

Webix Jet has four types of routers. The default router is _HashRouter_. If you don't want to display the hashbang in the URL, you can change the router to _UrlRouter_:

```javascript
// myapp.js
import "./styles/app.css";
import { JetApp, EmptyRouter, UrlRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            router: BUILD_AS_MODULE ? EmptyRouter : UrlRouter,    // !
            debug: !PRODUCTION,
            start: "/top/layout"
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

## Changing View Creation Logic

If you use Webix Jet 3.0+, which is build with Vite, you can use its dynamic import. If you use older versions or your custom Webpack setup, proceed to [Using with Webpack](part-iv-toolchain/webpack-configuration.md#changing-view-creation-logic).

After switching to Vite (Jet 3.0+), the app configuration requires a custom view loader, defined as a function or a direct assignment of an imported module.

For example, let’s say you have two folders in your project, “views” and “views_custom”, with the following file distribution:

```bash
views/
  - start.js
  - top.js
    - area/
        - list.js
```

Then you can use [multiple patterns](https://vitejs.dev/guide/features#multiple-patterns) in Vite’s `import.meta.glob` and provide the correct path to each module in views:

```js
// views/index.js
// note: because of the placement of this file in the project, 
// this relative path points to all files in "views" folder and all its subfolders

const modules = import.meta.glob(["./**/*.js"]);

// custom aliases
const aliases = {
    list: "area.list"
};

const views = name => {
	if (aliases[name]) name = aliases[name];
	const mod = modules[`./${name.replace(/\./g, "/")}.js`];
    if (!mod) return modules["./404.js"]().then((x) => x.default);
	return modules[path]().then(x => x.default);
};
```

```js
// sources/app.js
import {JetApp} from "webix-jet";
import { views } from "./views/index";

const app = new JetApp({
	start:		"/top/list",	// "list" should represent module from "views/area/list.js"
	views,
});

export default app;
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-start/blob/cb90f9e20bad52f08d83111c895fac40d745850b/sources/myapp.js#L4)

### Custom Logic of Creating Views

You can also implement your own logic of view creating. Define **views** as a function for that:

```javascript
// myapp.js
...
const defaults = {
    ...
    views: function(url){
        //implement your own logic here
        url = url.replace(/\./g, "/");
        const view = require("jet-views/"+url);
        if (view.__esModule) {
            view = view.default;
        }
        return view;
    }
};
...
```

## Beautifying the URL

If you do not want to display some paths, you can replace them with shorter URLs with the help of **routes** in the app configuration. For instance, you might want to display only the names of subviews in the URL:

```javascript
// myapp.js
import "./styles/app.css";
import {JetApp, EmptyRouter, HashRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            router	: BUILD_AS_MODULE ? EmptyRouter : HashRouter,
            debug	: !PRODUCTION,
            start	: "/top/layout",
            routes: {
                "/hi"	: "/top/about",
                "/form"	: "/top/area.left.form",
                "/list"	: "/top/area.list",
            }
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

Instead of a long URL with subdirectories and parent views, e.g. _"/top/area.left.form"_, the app URL will be displayed as _/form_.

{% hint style="warning" %}
Note that **routes** can be used to replaced the whole URL, not segments of the URL.
{% endhint %}

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/routes.js)

## Various App Parameters

In the app config, for example, you can set the mode in which the app will work:

```javascript
// app.js
import "./styles/app.css";
import {JetApp, EmptyRouter, HashRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            mode:"readonly",  //application wide configuration
            router: BUILD_AS_MODULE ? EmptyRouter : HashRouter,
            debug: !PRODUCTION,
            start: "/top/layout"
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );   // mandatory!
}
```

Later in the code, you can do some actions according to the mode:

```javascript
// views/games.js
...
if (this.app.config.mode === "readonly"){
    this.show("limited");
}
...
```

**this** refers to the current instance of a Jet view class \[1\].

## Adding Stylesheets

You can include several stylesheets. When the app will be built, all stylesheets will be compiled into _app.css_. _app.css_ will be added to _/codebase_.

```javascript
//app.js
import "./styles/app.css";  // !
import {JetApp, EmptyRouter, HashRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            router: BUILD_AS_MODULE ? EmptyRouter : HashRouter,
            debug: !PRODUCTION,
            start: "/top/layout"
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

## Footnotes

#### \[1\]:

To read more about how to reference apps and view classes, go to ["Referencing views"](referencing-views.md).

#### \[2\]:

Beginning from Webix Jet 1.6.

