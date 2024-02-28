# Configuring Webpack

Since Webix Jet 3.0, the default bundler we use in demos is Vite. Still, you can continue [using Webpack, click here to see the demo](https://github.com/webix-hub/jet-start/tree/webpack).

There are also some cases when you might want to add some extra configuration for your app. You can change the default Webpack configuration and get the expected result.

## Changing View Creation Logic

Use the **views** parameter to change the names of view modules inside your code.

For example, if the module you want to show is in a subfolder and you want to shorten the URL of the module, you can do it in the **views** parameter:

```javascript
// myapp.js
import "./styles/app.css";
import {JetApp, EmptyRouter, HashRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            router     : BUILD_AS_MODULE ? EmptyRouter : HashRouter,
            debug     : !PRODUCTION,
            start     : "/top/layout",
            views: {
                "start" : "area.list" // load /views/area/list.js
            }
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

In this example, **list** module is stored in the **area** subfolder in _/views_ \(_/views/area/list.js_\). Later, you can show the view by the new name, e.g.:

```javascript
// views/top
import {JetView} from "webix-jet";

export default class TopView extends JetView {
    config(){
        return {
            cols:[
                { view:"button", value:"start",
                  click:() => {
                    this.show("start");
                }},
                { $subview: true }
            ]
        };
    }
}
```

**this** in the button click handler refers to the current instance of a Jet view class [\[1\]](app-config.md#1).

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/viewresolve.js)

## Configuring Webix Jet to Work with a Backend Server

{% hint style="info" %}
These instructions are for the **development stage**. For production, [see this](../part-iv-toolchain/deploying-and-testing.md).
{% endhint %}

Let's look at an example of configuring Webix Jet to work with the Apache Tomcat server.

**Prerequisites:**

1. There is a Java app on your Tomcat server, configured to your needs.
2. There is a Webix Jet app \(you can use the [jet-start package](https://github.com/webix-hub/jet-start)\).

You need to let the Webix Jet app access the Java app. Set the **proxy** path in **webpack.config.js**. Replace this:

{% code-tabs %}
{% code-tabs-item title="webpack.config.js" %}
```javascript
devServer:{
    stats:"errors-only"
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

with this:

{% code-tabs %}
{% code-tabs-item title="webpack.config.js" %}
```javascript
devServer:{
    stats:"errors-only",
    proxy: {
        "/server":{
            target: 'http://localhost:9200',
            pathRewrite: {"^/server" : ""}
        }    
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

"[http://localhost:9200](http://localhost:9200)" is the web path to the Java server side.

This configuration will make it possible to set the path to the server as:

{% code-tabs %}
{% code-tabs-item title="models/mydata.js" %}
```javascript
export const mydata = new webix.DataCollection({        
    url:"/server/mydata"
})
```
{% endcode-tabs-item %}
{% endcode-tabs %}

This will load the data from "[http://localhost:9200/mydata](http://localhost:9200/mydata)".

Both servers can be run separately and at the same time. The Webix Jet app will be able to communicate with the server-side code as they both are run by the same server.

## Changing the Port of the Dev Server

You can change the default port 8080 to another port \(e.g. 3010\). You can do this by changing the [_package.json_](https://docs.npmjs.com/getting-started/using-a-package.json) file.

Modify the _"start"_ command in the _package.json_ file:

```javascript
// package.json
...
"scripts": { 
     ... 
     "start": "webpack-dev-server --port 3010"
}
```

## Multiple Start Files

By default, the app is built with one start file \(_admin.js_ in this example\):

{% code-tabs %}
{% code-tabs-item title="webpack.config.js" %}
```javascript
...
const config = {
    entry: "./sources/admin.js",
    output: {
        //...
        filename: "admin.js"
    },
    ...
}
...
```
{% endcode-tabs-item %}
{% endcode-tabs %}

To create multiple entry files, pass an object to **entry** in config. For output filenames, use the _\[name\]_ substitution to ensure that each file has a unique name:

{% code-tabs %}
{% code-tabs-item title="webpack.config.js" %}
```javascript
{
    entry: {
        admin: "./sources/admin.js",
        orders: "./sources/orders.js"
    },
    output: {
        //...
        filename: "[name].js"
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Turning Off Localization

If you do not want to localize the app and do not want to create an empty **locales** folder, you can change the Webpack config.

By default, Webpack tries to resolve the locales in _sources/locales_. Delete the `jet-locales` alias:

{% code-tabs %}
{% code-tabs-item title="webpack.config.js" %}
```javascript
const config = {
    ...
    resolve: {
        extensions: [".js"],
        modules: ["./sources", "node_modules"],
        alias:{
            "jet-views":path.resolve(__dirname, "sources/views"),
            "jet-locales":path.resolve(__dirname, "sources/locales") // !
        }
    },
    ...
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Next tell Webpack to ignore `jet-locales` while compiling the app. Use the [**IgnorePlugin**](https://webpack.js.org/plugins/ignore-plugin/) for this:

{% code-tabs %}
{% code-tabs-item title="webpack.config.js" %}
```javascript
...
plugins: [
    new MiniCssExtractPlugin({
        filename:"[name].css"
    }),
    new webpack.DefinePlugin({
        VERSION: `"${pack.version}"`,
        APPNAME: `"${pack.name}"`,
        PRODUCTION : production,
        BUILD_AS_MODULE : (asmodule || standalone)
    }),
    new webpack.IgnorePlugin(/jet-locales/)    // !
]
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Now the app will be compiled without the locales.

## Changing Paths tor Locales and Views

By default, views and locales are stores in **sources/views** and **sources/locales** correspondingly. If you want your app structure to be different, you can change the paths to views and locales in the Webpack config file:

{% code-tabs %}
{% code-tabs-item title="webpack.config.js" %}
```javascript
const config = {
    ...
    resolve: {
        extensions: [".js"],
        modules: ["./sources", "node_modules"],
        alias:{
            "jet-views":path.resolve(__dirname, "sources/components"),    // !
            "jet-locales":path.resolve(__dirname, "sources/languages")     // !
        }
    },
    ...
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Developing Big App with Webpack

Starting with Webix Jet 3.0, the toolchain has been migrated to Vite. However, you can still use Webpack.

## Modules and Large App Development

Webix Jet toolchain has been updated to support such kind of development (Starting with Webix Jet 1.5). For new projects, just use the [jet-start](https://github.com/webix-hub/jet-start/tree/webpack) pack.

To update existing projects:

- check **webpack.config.js** against the [latest one](https://github.com/webix-hub/jet-start/blob/webpack/webpack.config.js)
- update the **scripts** section in [package.json](https://github.com/webix-hub/jet-start/blob/master/package.json).

CLI commands:

| Command | What it does |
| :--- | :--- |
| `npm run module` or `yarn module` | builds a standalone module, which is stored in **dist/module/** \(a JS and a CSS files\); the module **doesn't** include _webix-jet_ |
| `npm run standalone` or `yarn standalone` | builds a standalone module, which is stored in **dist/full/** \(a JS and a CSS files\); the module **includes all dependencies** |

When the module is built, you can copy it to a subfolder of some other app, e.g. _sources/modules/_.

### When to use _module_

If you want to use the module as a part of another Webix Jet app:

* use _**npm run module**_ or _**yarn module**_
* import the JS and CSS files of your module from the subfolder you have put them into and initialize the app:

```javascript
// views/someview.js
import OtherApp from "modules/app-name";
import "modules/style.css";
...
config(){
    return {
        rows:[
            ToolbarView,
            new OtherApp({ app: this.app })
        ]
    };
}
```

{% hint style="info" %}
Be sure to use webix-jet 3.0+
{% endhint %}

Modules are much more lightweight than bundles with dependencies. So if you plan to create a lot of app modules, compile them this way.

### When to use _standalone_

If you want to use the module on a page without Webix Jet:

* `app.js` should contain explicit import of all views:

```javascript
export default class MyApp extends JetApp{
    constructor(config){
        const views = (name) => require("./views/"+name);
        const defaults = {
            /* ... */
            views,
            start   : "/top/start"
        };
        super({ ...defaults, ...config });
    }
}
```

* use _**npm run standalone**_ or _**yarn standalone**_

{% hint style="info" %}
The compiled sources should be copied to the main application or published on npm. Otherwise, if the sources are simply imported from one project to another, Webpack will try to use different webix-jet instances, while only one instance will ensure correct initialization.
{% endhint %}

* include the JS and CSS files of your module from the subfolder you have put them into:

```html
<!-- index.html -->
<script src="modules/app-name.js"></script>
<link rel="stylesheet" type="text/css" href="modules/style.css">
<script>
    webix.ready(function(){
        const app = new appname.default();
        app.render(document.body);
    });
</script>
```

Instead of `document.body` you can use the ID of the target HTML container.

Standalone bundles include all dependencies \(except _webix.js_\), so they are more stable. However, the size of a bundle is much bigger than the size of a module.

## Code Splitting

You can [split your code into bundles](https://webpack.js.org/guides/code-splitting/) and load them on demand, which can greatly influence the initial loading time of the application. Lazy loading of app code is possible in Webix Jet with the help of a custom **app.views** handler. Where you can import the bundle on demand [\[2\]](app-config.md#2).

```javascript
// myapp.js
import "./styles/app.css";
import {JetApp, EmptyRouter, HashRouter } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            router: BUILD_AS_MODULE ? EmptyRouter : HashRouter,
            debug: !PRODUCTION,
            start: "/top/layout",
            views: (name) => {
                if (name === "modules.clients") //sources/modules/
                    return import("modules/clients");

                // load all other modules with default strategy
                return name;
            }
        };

        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

In **webpack.config.js**, you can define the chunk naming scheme \(the _chunkFilename_ property\) in **output**:

```javascript
// webpack.config.js
...
output: {
    ...
    filename: "[name].js",
    chunkFilename: "[name].bundle.js"
    // will be 'clients.bundle.js' in this case
}
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/bundles/sources/bundles.js)

## Using Jet App as a Widget

If you want to nest an app module into Webix layout, you should create a custom widget based on **webix.ui.jetapp** and wrap the app into it.

```javascript
// sources/myapp.js
import {JetApp} from "webix-jet";
export default class MyApp extends JetApp {
    //app config
    constructor(){
        const defaults = {
            id     	: APPNAME,
			version	: VERSION,
            router  : BUILD_AS_MODULE ? EmptyRouter : HashRouter, //!
            debug   : !PRODUCTION,
            start   : "/top/start"
        };
        super({ ...defaults, ...config });
    }
}

if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}

// add this
webix.protoUI({
    name:"some-widget",
    app: MyApp
}, webix.ui.jetapp);
```

{% hint style="info" %}
Make sure the app config includes the EmptyRouter.
{% endhint %}

Now you can run `npm run standalone` or `yarn standalone` to get a standalone bundle. Then you can copy the bundle to any subfolder of your app and use it:

```html
<script src="module/app-name.js"></script>
<script>
    webix.ui({ view:"some-widget" })
</script>
```

This JetApp works as a widget and can be combined and sized like any other Webix UI widget.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/533f9ead6a41ce0715eefb9c568fab904f663424/sources/webixview.js)

## Dynamic Widget Loading

If you want to load custom widget code on demand, you can split your code and import the bundles with widget code when it is needed. For example, widgets can be imported in [config\(\)](../part-ii-webix-jet-in-details/views.md#config) of a Jet view:

```javascript
// views/statistics.js
import {JetView} from "webix-jet";
export default class StatisticsView extends JetView{
    config(){
        const widgets = import(/* webpackChunkName: "widgets" */ "modules/customgrid");
        return widgets.then(() => {

            return { rows:[
                { type:"header", template:"Sales 2018" },
                { view:"custom-grid" }
            ]};

        });
    }
}
```

{% hint style="info" %}
This works for both custom Webix widgets and Jet apps as custom widgets.
{% endhint %}

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/bundles/sources/bundles.js)

## Importing Webix as Module

We strongly recommend you to include Webix as a script, e.g.:

```html
<script type="text/javascript" src="//cdn.webix.com/edge/webix.js"></script>
<link rel="stylesheet" type="text/css" href="//cdn.webix.com/edge/webix.css">
```

But if you really need to include Webix as a module, you can do this in two ways.

### Building a Huge Bundle of Webix and the App

{% hint style="warning" %}
Please note that this way is not recommended, as the resulting file is relatively big and will work better as a separate script, without bundling in the single *app.js*.
{% endhint %}

1\. Make Webix available for all includes by adding the following section in **webpack.config.js**:

```javascript
plugins: [
	new webpack.ProvidePlugin({
		// SET PATH TO WEBIX HERE
		webix: path.join(__dirname, "node_modules", "@xbs", "webix-pro")
	}),
	// ...
]
```

2\. Import Webix:

```javascript
import { JetApp } from "webix-jet";
import * as webix from "webix"; // use script !!!

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            id: APPNAME,
            version: VERSION,
            debug: !PRODUCTION,
            start: "/top",
            webix: webix
       	};
        super({ ...defaults, ...config });
    }
}
```

## Troubleshooting

* If you get `can not resolve` error in webpack, make sure you add the alias and the path to the lib in question in **webpack.config.js**. [Check the docs](https://webpack.js.org/configuration/resolve/).
