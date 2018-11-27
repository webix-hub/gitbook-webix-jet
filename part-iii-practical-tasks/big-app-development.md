# Big App Development

## Modules and Large App Development

If you develop a large app, it has sense to spit it into multiple modules, which can be developed and tested separately and combined into a single app on the last step of development.

The Webix Jet toolchain has been updated to support such kind of development (**Starting with Webix Jet 1.5**). For new projects, just use the [jet-start](https://github.com/webix-hub/jet-start) pack. To update existing projects, check **webpack.config.js** against the [latest one](https://github.com/webix-hub/jet-start/blob/master/webpack.config.js) and update the **scripts** section in [package.json](https://github.com/webix-hub/jet-start/blob/master/package.json).

CLI commands:

| Command | What it does |
| :--- | :--- |
| `npm run module` or `yarn module` | builds a standalone module, which is saved to **codebase** \(a JS and a CSS files\); the module **doesn't** include _webix-jet_ |
| `npm run standalone` or `yarn standalone` | builds a standalone module, which is saved to **codebase** \(a JS and a CSS files\); the module **includes all dependencies** |

When the module is built, you can copy it to a subfolder of some other app, e.g. _sources/modules/_.

### When to use _module_

If you want to use the module as a part of another Webix Jet app:

* use _**npm run module**_ / _**yarn module**_
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

### When to use _standalone_

If you want to use the module on a page without Webix Jet:

* use _**npm run standalone**_ / _**yarn standalone**_
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

Standalone bundles include all dependencies \(except _webix.js_\), so they are more stable. However, the size of a bundle is much bigger than the size of a module.

## Using Jet App as a Widget

If you want to nest an app module into a Webix layout, you should create a custom widget based on **webix.ui.jetapp** and wrap the app into it. Follow these steps:

1\. Make sure that app rendering is turned off for module builds;

```javascript
// sources/myapp.js
import {JetApp} from "webix-jet";
export default class MyApp extends JetApp {
    constructor(){
        const defaults = {
            id		: APPNAME,
            version	: VERSION,
            debug	: !PRODUCTION,
            start	: "/top/start"
        };
        super({ ...defaults, ...config });
    }
}

// this!
if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );
}
```

2\. run `npm run module / yarn module`;

3\. Include the files from the **codebase** folder into **index.html** and wrap the module in a custom widget based on **webix.ui.jetapp**;

```js
// index.html
webix.protoUI({
    name:"myBigWidget",
    app: MyApp
}, webix.ui.jetapp);
```

4\. Include the module in a Webix layout.

```js
// index.html
webix.ready(function(){
	webix.ui({
		type:"clean", rows:[
			{ view:"myBigWidget" },
			// ...
		]
	});
});
```

This JetApp works as a widget and can be combined and sized like any other Webix UI widget.

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/tree/master/sources/webixview.js)

