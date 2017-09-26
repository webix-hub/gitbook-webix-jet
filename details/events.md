## Views Communication. Events

The views are separated, but there should be some means of communication between them. Feel free to use the in-app event bus for that. You can attach an event handler to the event bus in one view and trigger the event in another view.

First, attach an event to one view:

~~~js
init(){
    on(app, "eventName", function(something){
        //handler
        this.show(something);
    });
}
~~~

In the other view, there should be code that triggers the event, e.g.:

~~~js
on:{
    onEvent:function(){
        app.callEvent("eventName", [some_value]);
    }
}
~~~

### Aliases (?)

To make your life happier, there are aliases for methods used to trigger events.

1. *trigger*

Instead of using

~~~js
app.callEvent("eventName", [some_value]);
~~~

you can write

~~~js
app.trigger("eventName", [some_value]);
~~~

2. *action*

This alias unites both the click handler and the callEvent method. So the same code transforms into:

~~~js
on:{
    onEventName:app.action("eventName", [some_value]);
}
~~~

### Declaring and calling methods (?)

One more effective way of connecting views is methods. In one of the views we define a handler that will call some function and in another view we call this handler.

Unlike events, methods both call actions in views and are able to return something useful. However, this option can only be used when we know that a view with the necessary method exists. It's better to use this variant with a parent and a child views. A method is declared in the child view and is called in the parent one.

Let's have a look at the example below:

~~~js
/* views/actions.js */
export class Actions extends JetView {
    config() {
        return {
            view:"datatable", localId:"actions", autoConfig:true, autoheight:true, scroll:false,
            data:[
                { action:"UnSubscribed from all lists", date: "2017-07-12" },
                { action:"Subscribed to the News list", date: "2017-06-08" },
                { action:"Subscribed to the Marketing list", date: "2017-06-04" }
            ]
        }
    }
    truncateAll(){
        this.$$("actions").clearAll();
    }
};
~~~

The code of a view creates a datatable. It this view we declare the **truncateAll()** method for clearing the table. Its parent view should call this method:

~~~js
/* views/demo.js */
import {Actions} from "actions"
export class DemoView extends JetView {
    config(){
        return {
            rows: [
                { view:"toolbar", elements: [
                    { view:"button", value:"Clear all",
                    click:()=>{
                        //this.app.$$("actions").clearAll();
                        Actions.truncateAll();
                        //то что сверху в комментах работает, но не знаю, как ссылаться на другой вью, чтобы truncateAll вызвать.
                    } }
                ] },
                Actions
            ]
        }
    }
}
/* app.js */
import {DemoView} from "demo"
var app = new JetApp({
    start: "/Demo",
    views: {
        "Demo": DemoView,
        "Actions": Actions
    }
}).render();
~~~

If you click the button, all the records from the datatable will be deleted.