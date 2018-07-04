# Big App Development

## Modules and Large App Development

If you develop a large app, it has sense to spit it into multiple modules, which can be developed and tested separately and combined into a single app on the last step of development.

Webix Jet toolchain has been updated to support such kind of development [\[1\]](big-app-development.md#1). For new projects, just use the [jet-start](https://github.com/webix-hub/jet-start) pack. To update existing projects, check **webpack.config.js** against the [latest one](https://github.com/webix-hub/jet-start/blob/master/webpack.config.js) and update the **scripts** section in [package.json](https://github.com/webix-hub/jet-start/blob/master/package.json).

CLI commands:

| Command | What it does |
| --- | --- |
| _npm run module_ | builds a standalone module, which is stored in **dist/{modulename}.js**; the module **doesn't** include _webix-jet_ |
| _npm run standalone_ | builds a standalone module, which is stored in **dist/{modulename}.js**; the module **includes all dependencies** |

### When to use _npm run module_

If you want to use the module as a part of another Webix Jet app:

* use _**npm run module**_
* import files in **dist** from the master project:

```javascript
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

### When to use _npm run standalone_

If you want to use the module on a page without Webix Jet:

* use _**npm run standalone**_
* just include the resulting JS file:

```html
<!-- index.html -->
<script src="../other-app/dist/full/app-name"></script>
<script>
    var app = new appname.default();
    app.render(document.body);
</script>
```

Standalone bundles are including all dependencies, so they are more stable \( except of webix.js \). However, the size of a bundle is much bigger than the size of a module.

## Using Jet App as a Widget

If you want to nest an app module into Webix layout, you should create a custom widget based on **webix.ui.jetapp** and wrap the app into it.

```javascript
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

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/tree/master/sources/webixview.js)

## Footnotes

#### \[1\]:

Starting with Webix Jet 1.5

