# Using with Vite

You can use Jet with other app bundlers instead of WebPack. For example, [Vite](https://vitejs.dev/). There are some differences from usage with WebPack:

- the `.env` file is added, it stores global constants:

{% code-tabs %}
{% code-tabs-item title=".env" %}
```
APPNAME=Demo
VERSION=1.0.0
BUILD_AS_MODULE=false
```
{% endcode-tabs-item %}
{% endcode-tabs %}

To refer to the constants, use `import.meta.env.{name}`:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
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
{% endcode-tabs-item %}
{% endcode-tabs %}

- the `app.js` contains section that imports all files from the "views" folder and assigns custom view resolver to the app class:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
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
{% endcode-tabs-item %}
{% endcode-tabs %}

- optional, app.js contains custom locale loader:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
const locales = import.meta.glob("./locales/*.js");
const words = name => locales[`./locales/${name}.js`]().then(x => x.default);

...

export default class MyApp extends JetApp{
	constructor(config){
        //...

        // patch locales loader, optional
        this.use(plugins.Locale, { path: false, storage: this.webix.storage.session });
        const ls = this.getService("locale");
        ls.setLang = (lang, silent) =>
            words(lang).then(w => ls.setLangData(lang, w, silent));
        ls.setLang(ls.getLang(), true);
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

- replace old scripts:

{% code-tabs %}
{% code-tabs-item title="package.json" %}
```json
"scripts": {
    "build": "vite build",
    "start": "vite"
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

- the dev server is at ```http://localhost:5173```.

You can see the full code of the demo in the [vite branch of jet-start](https://github.com/webix-hub/jet-start/tree/vite).
