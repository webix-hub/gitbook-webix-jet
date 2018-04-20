<style>
.code{
    font-family: Consolas, monospace;
}
</style>

# <span id="contents">Testing and Deployment</span>

- [Deploying the App](#production)
- [Testing the App](#testing)
- [Modules and Large App Development](#modules)
- [Using Jet App as a Widget](#app_widget)

## [<span id="production">Deploying the App &uarr;</span>](#contents)

When your app is complete, you need to build it for production.

If you are using *webpack.config.js* from the **jet-start** project, you can run either of these commands:

```bash
npm run build
```

or

```bash
webpack --env.production true
``` 

Any of this commands will compile the whole app in two files, **myapp.css** and **myapp.js**, which will be stored in the **codebase** folder (names may differ, based on the webpack config).

Now, you need to upload the files from **codebase** and **index.html** (the html page which is stored at the root of the project) to the production server.

By default, **index.html** uses the CDN version of Webix. If you are using Webix PRO, you need to change paths in **index.html** to the place where *webix.js* and *webix.css* are stored on the production server. 

After building an app, you need to include **webix.js** and **codebase/myapp.js**. There are no extra dependencies.

## [<span id="testing">Testing the App &uarr;</span>](#contents)

To check the quality of the source code, run the following command:

```bash
npm run lint
```

## [<span id="modules">Modules and Large App Development &uarr;</span>](#contents)

If you develop a large app, it has sense to spit it into multiple modules, which can be developed and tested separately and combined into a single app on the last step of development.

Webix Jet toolchain has been updated to support such kind of development<sup><a href="#footnote1" id="origin1">*</a></sup>. If you are starting a new project, just use [jet-start](https://github.com/webix-hub/jet-start). If you want to update existing projects, you need to check **webpack.config.js** against the [latest one](https://github.com/webix-hub/jet-start/blob/master/webpack.config.js) and update the **scripts** section in [package.json](https://github.com/webix-hub/jet-start/blob/master/package.json).

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

Modules are much more lightweight. So if you plan to create a lot of app modules, compile them this way.

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

To be able to nest an app module into Webix layout, you should create a custom widget based on **webix.ui.jetapp** and wrap the app into it.

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
