# Creating App

A Webix Jet app is single-page and is divided into views. The application structure consists of the following parts:

- the *index.html* file that is a start page of the app;
- *sources/myapp.js* that contains the configuration of the app, you can give any name to this file;
- the *sources/views* folder that contains elements of the interface;
- the *sources/models* folder that contains data modules.

An app represents an application or an application module. It is used to group views and modules which implement some specific scenario. Later you will be able to combine separate app modules in high-level apps.

### The Syntax of Creation

An app module is created as a new instance of the JetApp class.

1. Import the _JetApp_ class and create its instance.
2. Define the start point, which is the start app URL. App configuration can include other properties like the app name, version, etc.
3. Call the **render()** method of the JetApp instance to paint the UI on the page.
4. Enclose it in **webix.ready()** to ensure that HTML page is parsed completely before JS starts executing.

~~~js
// myapp.js
import {JetApp} from "webix-jet";

webix.ready(function(){
	var app = new JetApp({
		start:"/top/layout"
	}).render(); //mandatory!
});
~~~

You can open the needed URL and the UI will be rendered from the URL elements. The app splits the URL into parts, finds the corresponding files in the **views** folder and creates an interface by combining UI modules from those files.

### App Configuration

In the app configuration, for example, you can set the mode in which the app will work:

```js
// myapp.js
import {JetApp} from "webix-jet";

var app = new JetApp({
	mode:"readonly",  //application wide configuration
	start:"/top/layout"
});
```

Later in the code, you can do some actions according to the mode:

```js
// views/games.js
...
if (this.app.config.mode === "readonly"){
	this.show("limited");
}
...
```

**this** refers to the current instance of a Jet view (_games_, for example)<sup><a href="#myfootnote1" id="origin">1</a></sup>.

### Routers

Webix Jet has four types of routers. You should specify the preferred router in the app configuration as well. The default router is _HashRouter_. If you don't want to display the hashbang in the URL, you can change the router to _UrlRouter_:

```js
// myapp.js
import {UrlRouter,JetApp} from "webix-jet";

var app = new JetApp({
	router: UrlRouter,
    start:"/top/layout"
});
```

## Further reading

You can read these sections of Part II:

- [App Config](../details/app_config.md)
- [Routers](../details/routers.md)
- [JetApp API](../details/app.md)
- [Webpack Configuration](../details/webpackconfig.md)

<!-- footnotes -->
- - -
<a id="myfootnote1" href="#origin">1 &uarr;</a>:
You can read more about ["Referencing views"](../details/referencing.md) and apps.