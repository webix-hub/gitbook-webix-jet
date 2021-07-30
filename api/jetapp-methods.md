# JetApp Methods

## app.attachEvent\(\)

Use this method to attach an event listener.

**Parameters:**

- *name* (string) is the name of the event,
- *handler* (function) the handler for the event.

**Returns:** nothing.

```javascript
// myapp.js
...
app.attachEvent("app:guard", function(url, view, nav){
    if (url.indexOf("/blocked") !== -1)
        nav.redirect="/somewhere/else";
});
...
```

More details on events:

- ["JetApp Events"](./jetapp-events.md)
- ["View Communication"](../part-ii-webix-jet-in-details/view-communication.md)
- ["Inner Events and Error Handling"](../part-ii-webix-jet-in-details/inner-events-and-error-handling.md).

## app.callEvent\(\)

Use this method to call a custom event.

**Parameters:**

- *name* (string) is the name of the event,
- *params* (array) is the array of parameters.

**Returns:** a boolean.

```javascript
// views/data.js
import {JetView} from "webix-jet";

export default class DataView extends JetView{
    config(){
        return {
            view:"button", click:() =>
                this.app.callEvent("save:form")
        }
    }
}
```

Normally, inner events are called automatically, so there is no need to use **callEvent** for them.

More details on events:

- ["JetApp Events"](./jetapp-events.md)
- ["View Communication"](../part-ii-webix-jet-in-details/view-communication.md)
- ["Inner Events and Error Handling"](../part-ii-webix-jet-in-details/inner-events-and-error-handling.md).

## app.contains()

The method returns *true* if the app contains the view from the parameter and *false* if not.

**Parameters:**

- **view** - a Jet view.

**Returns:** a boolean.

## app.createView()

The **createView** method creates a Jet view based on the UI configuration passed to it as a parameter.

```js
let view = this.app.createView(FormView); // FormView is a Jet view class
this.getRoot().addView(view);
```

**Parameters:**

- **ui** - the UI configuration of the view. It can be defined as:
	- a Webix view object
	- a Jet view class
	- a Jet app class constructor call
	- a Jet view class constructor call
	- a factory function that returns a Webix view object
- **name** (string, optional) - the name of the view

**Returns:**

- **view** (object) - the newly created view (instance of a Jet view class)

## app.getService\(\)

The method returns a service.

Parameters:

- **name** (string) - the name of the service

Call this method to use a service:

```javascript
// views/form.js
import {JetView} from "webix-jet";
import {getData} from "models/records";

export default class FormView extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" }
            ]
        };
    }
    init(){
        const id = this.app.getService("masterTree").getSelected();
        this.getRoot().setValues(getData(id));
    }
}
```

You can read more about services in ["View Communication"](view-communication.md).

## app.getSubView()

The method returns the current top view of the app.

**Parameters:** none.

**Returns:** a Jet view (object).

```js
// views/some.js
import {JetView} from "webix-jet";
export default class FormView extends JetView{
    config(){
        return {/* ...UI */};
	}
	init(){
		const top = this.app.getSubView();
	}
}
```

## app.getUrl()

The method **returns** the app URL as an array of objects, each containing:

- **index** (number) - the index of the URL segment,
- **page** (string) - the segment itself,
- **params** (object) - the list of URL parameters.

```javascript
this.app.getUrl();
/* [
	{ index:1, page:"top", params:{} },
	{ index:2, page:"some", params:{} }
]*/
```

## app.refresh\(\)

If you want to repaint the UI of the application, call _app.refresh\(\)_. It will re-render all the views.

**Parameters:** none.

**Returns:** a promise.

## app.render\(\)

The **render** method builds the UI of the application. If called without any parameters, it just renders the UI of the top view inside the page according to the start URL, specified in the app configuration.

**Optional parameters:**

- **container** (string, HTMLElement, SubView object) where the app will be rendered
- **url** (array,string) - URL segments as objects with *page*, *params*, and *index* or a URL as a string

**Returns:** a promise.

```javascript
// myapp.js
...
app.render();
```

If you want to render the app inside a container, you can pass the ID of the container or an HTMLElement:

```javascript
// myapp.js
...
app.render("mybox");
```

This is how you can define the URL of the app with this method:

```javascript
app.render(document.body, "/top/start");
```

## app.setService\(\)

The method creates a service.

**Parameters:**

- *name* (string) is the name of the service,
- *obj* (object) includes all methods and properties of the service that will be available to any view in the app.

You can create services in views:

```javascript
// views/tree.js
import {JetView} from "webix-jet";

export default class TreeView extends JetView{
    config(){
        return { view:"tree" };
    }
    init() {
        this.app.setService("masterTree", {
            getSelected : () => this.getRoot().getSelectedId();
        });
    }
}
```

**this** refers to the instance of the _treeView_ class if it is used in an _arrow function_ [\[1\]](jetapp-methods.md#1).

You can also create services in app constructor:

```javascript
// myapp.js
export default class MyApp extends JetApp{
    constructor(config){
        // ...config, plugins

        this.setService("timer", {
            startTimer(){
                this.start = new Date();
            }
        });
    }
}
```

To access the service, use the [getService](jetapp-methods.md#app-getservice) method.

You can read more about services in ["View Communication"](../part-ii-webix-jet-in-details/view-communication.md).

## app.show\(\)

The **show** method is used to change the app interface. This method rebuilds the whole UI of the app according to the URL passed as a parameter.

**Parameters:**

- *path* (string) is the URL according to which you want to change the UI of the app

**Returns:** a promise.

```javascript
// views/some.js
...
app.show("/demo/details");
```

For more info about showing UI components, visit ["Navigation"](../part-ii-webix-jet-in-details/navigation.md).

## app.use\(\)

The **use** method is used to switch on plugins.

**Parameters:**

* *plugin* is a Jet plugin (default or custom)
* the plugin *configuration* (see details for each plugin in ["Plugins"](../part-ii-webix-jet-in-details/plugins.md))

**Returns:** nothing.

```javascript
// myapp.js
import { plugins } from "webix-jet";
import session from "models/session";
...
app.use(plugins.User, { model: session });
```

## Footnotes

#### \[1\]:

To read more about how to reference apps and view classes, go to ["Referencing views"](../part-ii-webix-jet-in-details/referencing-views.md).
