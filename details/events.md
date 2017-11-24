# View Communication

Views are separated, but there should be some means of communication between them.

- [Parameters](#params)
- [Events](#events)
- [Methods](#methods)

### <span id="params">Parameters</span>

You can enable view communication with *parameters*. For instance, you need to open a form with some specific data from another view. Pass the needed parameters to *view.show*:

```js
// views/data.js
export default class DataView extends JetView{
    config(){
        return { rows:[
            { $subview:true }
        ]};
    }
    init(){
        this.show("./form?id=1");
    }
}
```

And here is **form**:

```js
// views/form.js
import {JetView} from "webix-jet";
import {getData} from "../models/records";

export default class FormView extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" },
                { view:"text", name:"email" }
            ]
        };
    }
    urlChange(view,url){
        if(url[0].params.id){
            this.getRoot().setValues( getData(id) );
        }
    }
}
```

In this simple example, as soon as DataView is initialized, the form is filled with data from a data record with ID 1.

You can also pass several parameters to **show**:

```js
this.show("./form?name=Jack&email=some");
```

### <span id="events">Events</span>

Feel free to use the in-app event bus for view communication.

##### Calling an Event

To trigger an event, call **app.callEvent**. You can call the method by referencing the app with **this.app** from an *arrow function*<sup><a href="#myfootnote1" id="origin">1</a></sup>:

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

##### Attaching an Event

You can attach an event handler to the event bus in one view and trigger the event in another view.

This is how you can attach an event to a Jet view:

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

One more way to attach an event is **this.on** (**this** references a Jet view). This way is better, because the event is automatically detached when the view that called it is destroyed.

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    init(){
        this.on(this.app, "save:form", function(){
            this.show("aftersave");
        });
    }
}
```

Once an event is attached, any other view can call it.

### <span id="methods">Declaring and Calling Methods<span>

One more effective way of connecting views is methods. In one of the views, you can define a method, and in another view, we you can call this method.

Unlike events, methods can also return something useful. Methods can only be used for view communication when you know that a view with the necessary method exists. It's better to use this variant with a parent and a child views. A method is declared in the child view and is called in the parent one.

##### Events vs Methods

Have a look at the example. Here is a view that has the *setMode("mode")* method:

```js
// views/child.js
import {JetView} from "webix-jet";

export default class ChildView extends JetView{
    cofig(){
        return {
            { view:"spreadsheet" }
        }
    }
    setMode("mode"){
        //sets the mode of the view
    }
}
```

And this is a parent view that will include *ChildView* and call its method:

```js
// views/parent.js
import {JetView} from "webix-jet";
import ChildView from "child";

export default class ParentView extends JetView{
    config() {
        return {
            rows:[
                { view:"button", value:"Set mode", click:() => {
                    this.getSubView().setMode("readonly")}
                }, 
                { $subview: ChildView }]
    }}
}
```

**this.getSubView()** refers to *ChildView* and calls the method. **getSubView()** can take a parameter with the name of a subview if there are several subviews, as you will see in the next section.

You can use methods for view communication in similar use-cases, but still events are more advisable here. Now let's have a look at the example where methods are better then events.

##### Methods vs Events

Suppose you want to create a file manager resembling Total Commander. The parent view will have two *file* views as subviews:

```js
// views/manager.js
import FileView from "files";
...
config() { 
    return { 
        cols:[ 
            { name:"left", $subview:FileView }, 
            { name:"right", $subview:FileView }
        ]
}}
```

Here each subview has a name. *FileView* has the *loadFiles* method. Next, let's tell the file manager which paths to open in each file view:

```js
// views/manager.js

init() {
	this.getSubView("left").loadFiles("a");
	this.getSubView("right").loadFiles("b");
}
```

Both subviews are referenced with **getSubView(name)**.

## Further reading

For more info on view communications, you can read:

- [Services](services.md)

<!-- footnotes -->
- - -
<a id="myfootnote1" href="#origin">2 &uarr;</a>:
To read more about how to reference apps and view classes, go to ["Referencing views"](../detailed/referencing.md).