# JetApp API

Here you can find the list of all the **JetApp** methods, that you can make use of.

| Method | Use to  |
|--------|---------|
| [attachEvent(event, handler)](#attach)   | attach an event |
| [callEvent(event)](#call)                | call an event |
| [getService(name,handler)](#get_service) | access a service by its name |
| [render(container)](#render)             | render the app or the app module |
| [setService(name)](#set_service)         | set a service |
| [show(url)](#show)                       | rebuild the app or app module according to the new URL |
| [use(plugin, config)](#use)              | switch on a plugin |

### <span id="attach">app.attachEvent("event:name", handler) </span>

Use this method to attach an event:

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    init(){
        this.app.attachEvent("save:form", function(){
            this.show("aftersave");
        });
    }
}
```

or

```js
// myapp.js
...
app.attachEvent("app:guard", function(url, view, nav){
    if (url.indexOf("/blocked") !== -1)
        nav.redirect="/somewhere/else";
});
...
```

For more details on events, read ["Events and Methods"](events.md) and ["Inner Events and Error Handling"](inner_events.md).

### <span id="call">app.callEvent("event:name")</span>

Use this method to call an event:

```js
// views/data.js
import {JetView} from "webix-jet";

export default class DataView extends JetView{
    config(){
        return {
            view:"button", click:() => {
                this.app.callEvent("save:form");
            }
        }
    }
}
```

For more details on events, read ["Events and Methods"](events.md) and ["Inner Events and Error Handling"](inner_events.md).

### <span id="get_service">app.getService(name)</span>

The method returns a service by its name, passed to the method as a parameter. Call this method to use a service:

```js
// views/form.js
import {JetView} from "webix-jet";
import {getData} from "../models/records";

export default class FormView extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" }
            ]
        };
    }
    init(){
        var id = this.app.getService("masterTree").getSelected();
        this.getRoot().setValues(getData(id));
    }
}
```

You can read more about services in the ["Services"](services.md) chapter.

### <span id="render">app.render()</span>

The **render** method builds the UI of the application. If called without any parameters, it just renders the UI according to the start URL, specified in the app configuration inside the page.

```js
// myapp.js
...
app.render();
```

But if you want to render the app inside a container, you can pass the string parameter to it with the ID of the container:

```js
// myapp.js
...
app.render("mybox");
```

### <span id="set_service">app.setService(name,handler)</span>

The method initializes a service for view communication.

```js
// views/tree.js
import {JetView} from "webix-jet";

export default class treeView extends JetView{
    config(){
        return { view:"tree" };
    }
    init() {
        this.app.setService("masterTree", {
            getSelected : () => this.getRoot().getSelectedId();
        })
    }
}
```

You can read more about services in the ["Services"](services.md) chapter.

### <span id="show">app.show(url)</span>

The **show** method is used to change the interface. This method rebuilds the whole UI of the app according to the URL passed as a parameter:

```js
// views/some.js
...
app.show("/demo/details")
```

For more info about showing UI components, visit the ["Navigation"](navigation.md) chapter.

### <span id="use">app.use(plugin, config)</span>

The **use** method is used to switch on plugins. The method takes two parameters:

- the name of the plugin 
- the plugin configuration

~~~js
//myapp.js
...
app.use(JetApp.plugins.PluginName, {
    /* config */
});
~~~

For more details, go to the ["Plugins"](plugins.md) chapter.

