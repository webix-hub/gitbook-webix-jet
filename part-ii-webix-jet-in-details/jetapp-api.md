# JetApp API

Here you can find the list of all the **JetApp** methods, that you can make use of.

| Method | Use to |
| --- | --- |
| attachEvent\(event, handler\) | attach an event |
| callEvent\(event\) | call an event |
| detachEvent\(event\) | manually detach an event listener |
| getService\(name,handler\) | access a service by its name |
| refresh\(\) | repaints the UI of an app |
| render\(container\) | render the app or the app module |
| setService\(name\) | set a service |
| show\(url\) | rebuild the app or app module according to the new URL |
| use\(plugin, config\) | switch on a plugin |

## app.attachEvent\(\)

Use this method to attach a custom event, for example, from a Jet view class:

```javascript
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

Events can be attached both in the app file and in view modules. Yet, if you attach an event listener with this method, you had better to detach it manually with **detachEvent\(\)** to eliminate a possibility of memory leaks.

You can also attach an inner Jet event. For instance, from app:

```javascript
// myapp.js
...
app.attachEvent("app:guard", function(url, view, nav){
    if (url.indexOf("/blocked") !== -1)
        nav.redirect="/somewhere/else";
});
...
```

For more details on events, read ["View Communication"](view-communication.md) and ["Inner Events and Error Handling"](inner-events-and-error-handling.md).

## app.callEvent\(\)

Use this method to call a custom event:

```javascript
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

Normally, inner events are called automatically, so there is no need to use **callEvent** for them.

For more details on events, read ["View Communication"](view-communication.md) and ["Inner Events and Error Handling"](inner-events-and-error-handling.md).

## app.detachEvent\(\)

Use this method to detach event listeners added by **attachEvent** from Jet view classes. You should do this, because the lifetime of such an event listener is longer than the lifetime of the Jet view. So if the view is destroyed, but the event listener isn't detached, this may cause memory leaks, especially in older browsers.

To detach an event, call **app.detachEvent** when the view that attached the event is destroyed:

```javascript
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    init(){
        this.app.attachEvent("save:form", function(){
            this.show("aftersave");
        });
    }
    destroy(){
        this.app.detachEvent("save:form");
    }
}
```

For more details on events, read ["View Communication"](view-communication.md) and ["Inner Events and Error Handling"](inner-events-and-error-handling.md).

## app.getService\(\)

The method returns a service by its name, passed to the method as a parameter. Call this method to use a service:

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
        var id = this.app.getService("masterTree").getSelected();
        this.getRoot().setValues(getData(id));
    }
}
```

You can read more about services in the ["View Communication"](view-communication.md) chapter.

## app.refresh\(\)

If you want to repaint the UI of the application, call _app.refresh\(\)_. It will re-render all the views.

## app.render\(\)

The **render** method builds the UI of the application. If called without any parameters, it just renders the UI inside the page according to the start URL, specified in the app configuration.

```javascript
// myapp.js
...
app.render();
```

But if you want to render the app inside a container, you can pass the string parameter to it with the ID of the container:

```javascript
// myapp.js
...
app.render("mybox");
```

## app.setService\(\)

The method initializes a service for view communication.

```javascript
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

**this** refers to the instance of the _treeView_ class if it is used in an _arrow function_ [\[1\]](jetapp-api.md#1).

You can read more about services in the ["View Communication"](view-communication.md) chapter.

## app.show\(\)

The **show** method is used to change the app interface. This method rebuilds the whole UI of the app according to the URL passed as a parameter:

```javascript
// views/some.js
...
app.show("/demo/details")
```

For more info about showing UI components, visit the ["Navigation"](navigation.md) chapter.

## app.use\(\)

The **use** method is used to switch on plugins. The method takes two parameters:

* the name of the plugin 
* the plugin configuration

```javascript
// myapp.js
import session from "models/session";
...
app.use(plugins.User, { model: session });
```

For more details, go to the ["Plugins"](plugins.md) chapter.

### Footnotes

#### \[1\]:

To read more about how to reference apps and view classes, go to ["Referencing views"](referencing-views.md).

