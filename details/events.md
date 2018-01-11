# <span id="contents">View Communication</span>

Views are separated, but there should be some means of communication between them.

- [Parameters](#params)
- [Events](#events)
- [Methods](#methods)

### [<span id="params">Parameters &uarr;</span>](#contents)

You can enable view communication with *parameters*. This way of communication is useful if you want to initialize components and load data. You can pass parameters with the URL in the **show** method of a Jet view class.

#### Sending Parameters with *view.show*

For instance, let's create a view that open a form as a subview with some specific data. Pass the needed URL parameters to *view.show*:

```js
// views/data.js
export default class DataView extends JetView{
    config(){
        return { cols:[
            { $subview:true }
        ]};
    }
    init(){
        this.show("./form?id=1");
    }
}
```

#### Getting Parameters

To use *id* in the form, you need to get to the **url** parameter that is received by **init** and by **urlChange** of a Jet class view. **url** is an array of objects, each with three properties:

- page - the name of the URL element
- params - the parameters after the URL
- index - the number of the URL element

**params** is the one. If any parameters were passed, you can get to them with *url[n].params.{parameter}*, where *n* is the number of the URL object that came last.

Let's redefine **urlChange** of the Jet form view to use the *id* parameter and fill the form with the values from a data record with ID = 1:

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

In this simple example, as soon as DataView is initialized, **urlChange** of *FormView* is called and the form is filled correct data.

#### Several URL Parameters

You can also pass several parameters to **show**:

```js
this.show("./form?name=Jack&email=some");
```

### [<span id="events">Events &uarr;</span>](#contents)

Feel free to use the in-app event bus for view communication.

#### Calling an Event

To call/trigger an event, call **app.callEvent**. You can call the method by referencing the app with **this.app** from an *arrow function*<sup><a href="#myfootnote1" id="origin1">1</a></sup>:

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

Calling events is necessary mostly for your custom events. Inner Jet events and Webix events are called automatically (e.g. the *onItemClick* event of List).

#### Attaching an Event

You can attach an event handler to the event bus in one view and trigger the event in another view.

##### Preferable Way: this.on

The best way to attach an event is **this.on** (**this** references a Jet view). The benefit of this way is that the event handler is automatically detached when the view that attached the event handler is destroyed. **this.on** can *handle* app and Webix events.

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

##### One More Way: app.attachEvent

One more way to attach an event is to call **app.attachEvent**. This way you'll have to detach an event manually<sup><a href="#myfootnote2" id="origin2">2</a></sup>.

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

To detach an event, call **app.detachEvent** when the view that attached the event is destroyed:

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    destroy(){
        this.app.detachEvent("save:form");
    }
}
```

### [<span id="methods">Declaring and Calling Methods &uarr;<span>](#contents)

One more effective way of connecting views is methods. In one of the views, you can define a method, and in another view, you can call this method.

Unlike events, methods can also return something useful. Methods can only be used for view communication when you know that a view with the necessary method exists. It's better to use this variant with a parent and a child views. A method is declared in the child view and is called in the parent view.

#### Events vs Methods

Have a look at the example. Here is a view that has the *setMode(mode)* method:

```js
// views/child.js
import {JetView} from "webix-jet";

export default class ChildView extends JetView{
    cofig(){
        return {
            { view:"spreadsheet" }
        }
    }
    setMode(mode){
        this.app.config.mode = mode;
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
                { $subview:"child" }]
    }}
}
```

**this.getSubView()** refers to *ChildView* and calls the method. **getSubView()** can take a parameter with the name of a subview if there are several subviews, as you will see in the next section.

You can use methods for view communication in similar use-cases, but still events are more advisable here. Now let's have a look at the example where methods are better then events.

#### Methods vs Events

Suppose you want to create a file manager resembling Total Commander. The parent view will have two *file* subviews:

```js
// views/manager.js
import FileView from "files";
...
config() { 
    return { 
        cols:[ 
            { name:"left", $subview:"fileview" }, 
            { name:"right", $subview:"fileview" }
        ]
}}
```

Here each subview has a name. *fileview* has the *loadFiles* method. Next, let's tell the file manager which paths to open in each file view:

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
<a id="myfootnote1" href="#origin1">1 &uarr;</a>:
To read more about how to reference apps and view classes, go to ["Referencing views"](../detailed/referencing.md).

<a id="myfootnote2" href="#origin2">2 &uarr;</a>:
Event listeners created with **attachEvent** have longer lifetimes than views that attached them. That's why, before you destroy the view, you have to detach event listeners to prevent memory leaks. Do not leave this task to the garbage collector.