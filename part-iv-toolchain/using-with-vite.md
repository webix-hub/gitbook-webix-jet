# Using with Vite

Starting with Webix Jet 3.0, the toolchain has been migrated to Vite. Read more about it [here](https://webix.gitbook.io/webix-jet/part-iv-toolchain/big-app-development). There are some differences from usage with WebPack:

- the `.env` file is added, it stores global constants:

```bash
APPNAME=Demo
VERSION=1.0.0
BUILD_AS_MODULE=false
```

To refer to the constants, use `import.meta.env.{name}`:

```javascript
// app.js
const defaults = {
    id 		: import.meta.env.APPNAME,
    version : import.meta.env.VERSION,
    router 	: import.meta.env.BUILD_AS_MODULE ? EmptyRouter : HashRouter,
    debug 	: !import.meta.env.PROD,
    // ...
};

...

if (!import.meta.env.BUILD_AS_MODULE){
	webix.ready(() => new MyApp().render() );
}
```

- the `app.js` contains section that imports all files from the "views" folder and assigns custom view resolver to the app class:

```javascript
// app.js
// dynamic import of views
const modules = import.meta.glob("./views/**/*.js");
const views = name => modules[`./views/${name}.js`]().then(x => x.default);

export default class MyApp extends JetApp{
	constructor(config){
        const defaults = {
            // ...
            views,
        }
    }
}
```

- optional, app.js contains custom locale loader:

```javascript
// app.js
const locales = import.meta.glob("./locales/*.js");
const words = name => locales[`./locales/${name}.js`]().then(x => x.default);

...

export default class MyApp extends JetApp{
	constructor(config){
        //...

        // patch locales loader, optional
        this.use(plugins.Locale, {
			path: words,
			storage: this.webix.storage.session
		});
    }
}
```

- replace old scripts:

```json
// package.json
"scripts": {
    "build": "vite build",
    "start": "vite"
}
```

- the dev server is at ```http://localhost:5173```.

You can see the full code of the demo [here](https://github.com/webix-hub/jet-start/tree/master).
