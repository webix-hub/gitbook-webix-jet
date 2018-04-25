# Developing Large Applications

- [Modules and Large App Development](#modules)
- [Using Jet App as a Widget](#app_widget)

## [<span id="modules">Modules and Large App Development &uarr;</span>](#contents)

If you develop a large app, it has sense to spit it into multiple modules, which can be developed and tested separately and combined into a single app on the last step of development.

Webix Jet toolchain has been updated to support such kind of development<sup><a href="#footnote1" id="origin1">*</a></sup>. For new projects, just use the [jet-start](https://github.com/webix-hub/jet-start) pack. To update existing projects, check **webpack.config.js** against the [latest one](https://github.com/webix-hub/jet-start/blob/master/webpack.config.js) and update the **scripts** section in [package.json](https://github.com/webix-hub/jet-start/blob/master/package.json).

CLI commands:

| Command                                       | What it does |
|-----------------------------------------------|--------------|
| <span class="code">npm run module</span>      | builds a standalone module, which is stored in **dist/{modulename}.js**; the module <b>doesn't</b> include _webix-jet_ |
| <span class="code">npm run standalone</span>  | builds a standalone module, which is stored in **dist/{modulename}.js**; the module <b>includes all dependencies</b> |

### When to use <span class="code">npm run module</span>

If you want to use the module as a part of another Webix Jet app:

- use <span class="code">**npm run module**</span>
- import files in **dist** from the master project:

```js
// views/someview.js
import OtherApp from "../other-app/dist/module/app-name";
...
config(){
    return {
        rows:[
            ToolbarView,
            OtherApp
        ]
    };
}
```

Modules are much more lightweight than bundles with dependencies. So if you plan to create a lot of app modules, compile them this way.

### When to use <span class="code">npm run standalone</span>

If you want to use the module on a page without Webix Jet:

- use <span class="code">**npm run standalone**</span>
- just include the resulting JS file:

```html
<!-- index.html -->
<script src="../other-app/dist/full/app-name"></script>
<script>
    var app = new appname.default();
    app.render(document.body);
</script>
```

Standalone bundles are stable, because if they were tested, they will work in any other app they will be included. However, the size of a bundle is much bigger than the size of a module.

## [<span id="app_widget">Using Jet App as a Widget &uarr;</span>](#contents)

If you want to nest an app module into Webix layout, you should create a custom widget based on **webix.ui.jetapp** and wrap the app into it.

```js
// sources/myapp.js
import {JetApp} from "webix-jet";
export default class MyApp extends JetApp {
    //app config
    constructor(){
		super({
			start: "/top/form",
			router: EmptyRouter //!
		});
	}
}
// add this
webix.protoUI({
	name:"some-widget",
	app: MyApp
}, webix.ui.jetapp);
```

Now you can run **npm run standalone** and use the resulting app file like this:

```html
<script src="../other-app/dist/full/app-name"></script>
<script>
    webix.ui({ view:"some-widget" })
</script>
```

This JetApp works as a widget and can be combined and sized like any other Webix UI widget.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/tree/master/sources/webixview.js)

- - -
<a id="footnote1" href="#origin1">* &uarr;</a>:
Starting with Webix Jet 1.5