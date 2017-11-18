# App API

Here you can find the list of all the **JetApp** methods, that you can make use of.

| Method | Use to |
|--------|---------|
| [app.render()](#render)                       | render the app or the app module |
| [app.show(url)](#show)                        | rebuild the app or app module according to the new URL |
| [app.use(plugin, config)](#use)               | switch on a plugin |
| [callEvent("event:name")](#call)              | call an event |
| [attachEvent("event:name", handler)](#attach) | attach an event |

### <span id="render">app.render()</span>

The **render** method is the method that builds the UI of the application. If called without any parameters, it just renders the UI according to the start URL, specified in the app configuration. The app UI takes the whole page.

```js
app.render();
```

But if you want to render the app inside of a container, you can pass the string parameter to it with the ID of the container:

```js
app.render("mybox");
```

### <span id="show">app.show(url)</span>

The **show** method is used to change the interface. This method rebuilds the whole UI of the app according to the URL passed as a parameter:

```js
app.show("/demo/details")
```

For more info about showing UI components, [visit the section on view referencing](referencing.md).

### <span id="use">app.use(plugin, config)</span>

The **use** method is used to include plugins. The method takes two parameters:

- the name of the plugin 
- the plugin configuration

~~~js
this.use(JetApp.plugins.PluginName, {
    /* config */
});
~~~

For more details, [go to the dedicated section](plugins.md) of this chapter.

### <span id="call">callEvent("event:name")</span>

Use this method to call an event.

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

### <span id="attach">attachEvent("event:name", handler) </span>

Use this method to attach an event on the app level.

```js
// views/form.js
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
app.attachEvent("app:guard", function(url, view, nav){
    if (url.indexOf("/blocked") !== -1)
        nav.redirect="/somewhere/else";
})
```

For more details on events, read ["Events and Methods"](events.md) and ["Inner Events and Error Handling"](inner_events.md).