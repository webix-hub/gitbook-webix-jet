# Big App Development

## Modules and Large App Development

If you develop a large app, it has sense to split it into multiple modules, which can be developed and tested separately and combined into a single app on the last step of development.

Starting with Webix Jet 3.0, the toolchain has been migrated to Vite, and it is now possible to build apps as modules using Vite (read about building with Webpack [here](https://webix.gitbook.io/webix-jet/part-iv-toolchain/big-app-development-with-webpack)). If you have an existing project that uses Webpack and you wish to migrate to Vite, follow these steps:

- provide [vite.config.js](https://github.com/webix-hub/jet-start/blob/vite-standalone/vite.config.js)
- set env variables via the [.env](https://github.com/webix-hub/jet-start/blob/vite-standalone/.env) file
- update your JetApp entry file according to this structure - [myapp.js](https://github.com/webix-hub/jet-start/blob/vite-standalone/sources/myapp.js)
- update the scripts section in [package.json](https://github.com/webix-hub/jet-start/blob/vite-standalone/package.json) (set the vite build command in particular)

For new projects, please refer to the corresponding [jet-start](https://github.com/webix-hub/jet-start/tree/vite-standalone) branch.

CLI commands:

| Command | What it does |
| :--- | :--- |
| `yarn build` or `npm run build` | builds a standalone module, which is stored in **dist/assets/** \(a JS and a CSS file\); the module **doesn't** include _webix-jet_ by default|

If you want to include *webix-jet* in your app, you need to exclude *webix-jet* from external dependencies in [rollupOptions](https://rollupjs.org/configuration-options/#external) of your [vite.config.js](https://github.com/webix-hub/jet-start/blob/vite-standalone/vite.config.js#L13) file. You can also define a separate config for such builds.

When the module is built, you can copy it to a subfolder of some other app, e.g. _sources/modules/_.

### Building a bundle with no dependencies

If you want to use the module as a part of another Webix Jet app:

* use **yarn build** or **npm run build**
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

### Building a bundle with dependencies

* modify the [vite.config.js](https://github.com/webix-hub/jet-start/blob/vite-standalone/vite.config.js#L13) file to exclude *webix-jet* as an external dependency (or define a separate config for such builds)
* use **yarn build** or **npm run build**
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

## Using Jet App as a Widget

If you want to nest an app module into Webix layout, you should create a custom widget based on **webix.ui.jetapp** and wrap the app into it.

```javascript
// sources/myapp.js
import "./styles/app.css";
import {JetApp, EmptyRouter, HashRouter, plugins } from "webix-jet";

// dynamic import of views
const modules = import.meta.glob("./views/**/*.js");
const views = name => modules[`./views/${name}.js`]().then(x => x.default);

// locales, optional
const locales = import.meta.glob("./locales/*.js");
const words = name => locales[`./locales/${name}.js`]().then(x => x.default);

export default class MyApp extends JetApp{
	constructor(config){
		const defaults = {
			id 		: import.meta.env.VITE_APPNAME,
			version : import.meta.env.VITE_VERSION,
			router 	: import.meta.env.VITE_BUILD_AS_MODULE ? EmptyRouter : HashRouter,
			debug 	: !import.meta.env.PROD,
			start 	: "/top/start",
			// set custom view loader, mandatory
			views
		};

		super({ ...defaults, ...config });

		// locales plugin, optional
		this.use(plugins.Locale, {
			path: words,
			storage: this.webix.storage.session
		});
	}
}

if (!import.meta.env.VITE_BUILD_AS_MODULE){
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

Now you can run `yarn build` or `npm run build` (with the correct config) to get a standalone bundle. Then you can copy the bundle to any subfolder of your app and use it:

```html
<script src="module/app-name.js"></script>
<script>
    webix.ui({ view:"some-widget" })
</script>
```

This JetApp works as a widget and can be combined and sized like any other Webix UI widget.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/tree/master/sources/webixview.js)

