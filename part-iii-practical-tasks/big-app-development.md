# Big App Development

## Modules and Large App Development

If you develop a large app, it has sense to spit it into multiple modules, which can be developed and tested separately and combined into a single app on the last step of development.

Webix Jet toolchain has been updated to support such kind of development [\[1\]](big-app-development.md#1). For new projects, just use the [jet-start](https://github.com/webix-hub/jet-start) pack. To update existing projects, check **webpack.config.js** against the [latest one](https://github.com/webix-hub/jet-start/blob/master/webpack.config.js) and update the **scripts** section in [package.json](https://github.com/webix-hub/jet-start/blob/master/package.json).

CLI commands:

| Command | What it does |
| --- | --- |
| `npm run module` or `yarn module` | builds a standalone module, which is stored in **dist/module/** (a JS and a CSS files); the module **doesn't** include _webix-jet_ |
| `npm run standalone` or `yarn standalone` | builds a standalone module, which is stored in **dist/full/** (a JS and a CSS files); the module **includes all dependencies** |

When the module is built, you can copy it to a subfolder of some other app, e.g. *sources/modules/*.

### When to use _npm run module_

If you want to use the module as a part of another Webix Jet app:

* use _**npm run module**_
* import the JS and CSS files of your module from the subfolder you have put them into:

```javascript
// views/someview.js
import OtherApp from "modules/app-name";
import "modules/style.css";
...
config(){
    return {
        rows:[
            ToolbarView,
            new OtherApp()
        ]
    };
}
```

{% hint style="info" %}
Be sure to use webix-jet 1.4+
{% endhint %}

Modules are much more lightweight than bundles with dependencies. So if you plan to create a lot of app modules, compile them this way.

### When to use _npm run standalone_

If you want to use the module on a page without Webix Jet:

* use _**npm run standalone**_
* include the JS and CSS files of your module from the subfolder you have put them into:

```html
<!-- index.html -->
<script src="modules/app-name.js"></script>
<link rel="stylesheet" type="text/css" href="modules/style.css">
<script>
    webix.ready(function(){
        var app = new appname.default();
        app.render(document.body);
    });
</script>
```

Instead of `document.body` you can use the ID of the target HTML container.

Standalone bundles include all dependencies \(except *webix.js*\), so they are more stable. However, the size of a bundle is much bigger than the size of a module.

## Using Jet App as a Widget

If you want to nest an app module into Webix layout, you should create a custom widget based on **webix.ui.jetapp** and wrap the app into it.

```javascript
// sources/myapp.js
import {JetApp} from "webix-jet";
export default class MyApp extends JetApp {
    //app config
    constructor(){
        const defaults = {
			id 		: APPNAME,
			version : VERSION,
			router 	: BUILD_AS_MODULE ? EmptyRouter : HashRouter, //!
			debug 	: !PRODUCTION,
			start 	: "/top/start"
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

Now you can run `npm run standalone` to get a standalone bundle. Then you can copy the bundle to any subfolder of your app and use it:

```html
<script src="module/app-name.js"></script>
<script>
    webix.ui({ view:"some-widget" })
</script>
```

This JetApp works as a widget and can be combined and sized like any other Webix UI widget.

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/tree/master/sources/webixview.js)

## Footnotes

#### \[1\]:

Starting with Webix Jet 1.5

