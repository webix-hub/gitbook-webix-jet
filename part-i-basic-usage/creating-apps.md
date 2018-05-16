# Creating Apps

A Webix Jet app is single-page and is divided into views. The application structure consists of the following parts:

* the _index.html_ file that is a start page of the app;
* _sources/myapp.js_ that contains the configuration of the app, you can give any name to this file;
* the _sources/views_ folder that contains elements of the interface;
* the _sources/models_ folder that contains data modules.

An app represents an application or an application module. It is used to group views and modules which implement some specific scenario. Later you will be able to combine separate app modules in high-level apps.

## The Syntax of Creation

An app module is created as a new instance of the JetApp class.

1. Import the _JetApp_ class and create its instance.
2. Define the start point, which is the start app URL. App configuration can include other properties like the app name, version, etc.
3. Call the **render\(\)** method of the JetApp instance to paint the UI on the page.
4. Enclose it in **webix.ready\(\)** to ensure that HTML page is parsed completely before JS starts executing.

```javascript
// myapp.js
import {JetApp} from "webix-jet";

webix.ready(function(){
    var app = new JetApp({
        start:"/top/layout"
    }).render(); //mandatory!
});
```

You can open the needed URL and the UI will be rendered from the URL elements. The app splits the URL into parts, finds the corresponding files in the **views** folder and creates an interface by combining UI modules from those files.

## App Configuration

In the app configuration, for example, you can set the mode in which the app will work:

```javascript
// myapp.js
import {JetApp} from "webix-jet";

var app = new JetApp({
    mode:"readonly",  //application wide configuration
    start:"/top/layout"
});
```

Later in the code, you can do some actions according to the mode:

```javascript
// views/games.js
...
if (this.app.config.mode === "readonly"){
    this.show("limited");
}
...
```

**this** refers to the current instance of a Jet view \(_games_, for example\) [\[1\]](creating-apps.md#1).

## Routers

Webix Jet has four types of routers. You should specify the preferred router in the app configuration as well. The default router is _HashRouter_. If you don't want to display the hashbang in the URL, you can change the router to _UrlRouter_:

```javascript
// myapp.js
import {UrlRouter,JetApp} from "webix-jet";

var app = new JetApp({
    router: UrlRouter,
    start:"/top/layout"
});
```

## Further reading

You can read these sections of Part II:

* [App Config](../part-ii-webix-jet-in-details/app-config.md)
* [Routers](../part-ii-webix-jet-in-details/routers.md)
* [JetApp API](../part-ii-webix-jet-in-details/jetapp-api.md)
* [Webpack Configuration](../part-iii-practical-tasks/webpack-configuration.md)

## Footnotes

### \[1\]:

You can read more about ["Referencing views"](../part-ii-webix-jet-in-details/referencing-views.md) and apps.

