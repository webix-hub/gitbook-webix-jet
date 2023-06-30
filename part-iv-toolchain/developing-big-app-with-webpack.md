# Developing Big App with Webpack

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

[Check out the demo >>](https://github.com/webix-hub/jet-demos/tree/master/sources/webixview.js)
