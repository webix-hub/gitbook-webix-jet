Views are separated, but there should be some means of communication between them.

## URL Parameters

You can enable view communication with *URL parameters*. This way of communication is useful if you want to initialize components and load data. You can pass parameters with the URL in the **show()** method of a Jet view class.

### Sending Parameters with *view.show()*

For instance, let's create a view that opens a form as a subview with some specific data. Pass the needed URL parameters to *view.show()*:

```js
// views/data.js
import {JetView} from "webix-jet";

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

### Getting Parameters from Class Methods

To access *id* in the form, you need to get to the **url** parameter that is received by **init()**, **urlChange()** and **ready()** methods of a Jet class view. **url** is an array of objects, each with three properties:

- **page** - the name of the URL element
- **params** - the parameters after the URL
- **index** - the number of the URL element

If any parameters were passed, you can get to them with *url[n].params.{parameter}*, where *n* is the number of the URL segment.

Let's access the *id* parameter from **urlChange()** and fill the form with the values from a data record with ID = 1:

```js
// views/form.js
import {JetView} from "webix-jet";
import {getData} from "models/records";

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
            view.setValues(getData(id));
        }
    }
}
```

In this simple example, as soon as DataView is initialized, **urlChange()** of *FormView* is called and the form is filled correct data.

### Setting / Getting Parameters with JetView Methods

You can get URL parameters with [this.getParam()](views.md#this-getparam). **getParam()** lets the API access the URL parameters of the current view and its parent. This can be useful, as views and subviews quite often share a common parameter.

Consider a simple example with two views, a parent **page** and its subview **some**. The URL is:

```
#!/top/page?id=12/some
```

Where **id** is the parameter of the parent **page**.

This is how you can get **id** from the **page** view:

```js
//from page.js
var id = this.getParam("id"); //id == 12
```

And this is how you can get **id** from **some**:

```js
//from some.js
var id = this.getParam("id");       //id == ""
var id = this.getParam("id", true); //id == 12
```

For example, this is a parent view that puts the parameter into the URL and loads a subview:

```js
// views/details.js
import {JetView} from "webix-jet";

export default class DetailsView extends JetView {
	config(){
        return { cols:[
            { $subview:true }
        ]};
    }
    init(){
        this.show("?id=1/sub");
    }
}
```

Here **sub** is a Jet view that will access the parent's parameter and display it:

```js
// views/sub.js
import {JetView} from "webix-jet";

export default class sub extends JetView {
	config(){
		return {
			view:"template"
		};
	}
	urlChange(view){
		var id = this.getParam("id", true);
		view.setHTML("id="+id);
	}
}
```

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

Use [this.setParam()](views.md#this-setparam) method to set the URL parameters or to change a URL segment:

```js
this.setParam("mode", "12", true); // some?mode=12
```

**setParam()** has three parameters:
- the *name* of the URL parameter,
- the new *value*,
- (optional) *display*, if it is set to *true*, the parameter is displayed in the URL.

For example, this is how you can change a URL parameter with a widget inside a Jet view:

```js
// views/top.js
import {JetView} from "webix-jet";

export default class TopView extends JetView {
    config(){
        return {
            rows:[
                { view:"segmented", options:["full", "brief"], on:{
                    onChange: function(){
                        this.$scope.setParam("mode", this.getValue(), true);
                    }
                }}
            ]
        };
    }
}
```

**mode** will be set to either *full* or *brief* and the value will be displayed in the URL.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

Work with URL parameters can be simplified with the [UrlParam plugin](details/plugins.md).

### Several URL Parameters

You can also pass several parameters to **show()**:

```js
this.show("./form?name=Jack&email=some");
```

## Events

Feel free to use the in-app event bus for view communication.

### Calling an Event

To call/trigger an event, call **app.callEvent()**. You can call the method by referencing the app with **this.app()** from an *arrow function* [1]:

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

Calling events is necessary mostly for custom events. Inner Jet events and Webix events are called automatically (e.g. the *onItemClick* event of List).

### Attaching an Event

You can attach an event handler to the event bus in one view and trigger the event in another view.

#### Preferable Way: this.on()

The best way to attach an event is **this.on()** (**this** references a Jet view). The benefit of this way is that the event handler is automatically detached when the view that attached the event handler is destroyed. **this.on()** can *handle* app and Webix events.

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    ...
    init(){
        this.on(this.app, "save:form", function(){
            this.show("aftersave");
        });
    }
}
```

Once an event is attached, any other view can call it.

#### One More Way: app.attachEvent()

One more way to attach an event is to call **app.attachEvent()**. This way you will have to detach the event manually with **app.detachEvent()** [2].

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

To detach an event, call **app.detachEvent()** when the view that attached the event is destroyed:

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    ...
    destroy(){
        this.app.detachEvent("save:form");
    }
}
```

## Services

A service is a way to share functionality. You can create a service connected to one of the views, and other views can communicate with the view through the service. 

With ES6, the problem can also be solved with creating and requiring a module. If you create two apps that require the same module, there is only one instance of the shared code. Services are better, because a new instance of a service is created for each new instance of a Jet view class.

### Initializing Services

To set a service, call the **app.setService()** method. **setService()** requires two parameters:

- **service** - (string) the name of the service
- **obj** - (object) methods that you want to call from other Jet views

For example, let's create a *masterTree* view and let other views get the ID of a selected item in the master tree.

```js
// views/tree.js
import {JetView} from "webix-jet";

export default class treeView extends JetView{
    config(){
        return { view:"tree" };
    }
    init(view) {
        this.app.setService("masterTree", {
            getSelected : () => view.getSelectedId();
        })
    }
}
```

### Using Services

To get to the service and its methods, use the **app.getService()** method. **getService()** requires one parameter - the **name** of the service.

This is how you can get the ID of the selected node in the master tree and use it to fill a form with correct values:

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
    init(view){
        var id = this.app.getService("masterTree").getSelected();
        view.setValues(getData(id));
    }
}
```

Apart from view communication, services can be used for loading and saving data. [You can read about it in the "Models" chapter](models.md#4-services-as-data-sources).

You can also use services to create app-level plugins. 

```js
//helpers/state.js
export function State(app){
    const service = {
        getState(){
            return this.state;
        },
        setState(state){
            this.state = state;
        },
        state:0
    };
    app.setService("state", service);
}
```

Such services should be included into the application in the start file (e.g. _myapp.js_) and enabled with **app.use()**:

```js
//myapp.js
import {state} from "helpers/state.js";
    
const app = new JetApp({...}).render();
app.use(state);
```

Any view will be able to use the service as:

```js
{ view:"button", value:"Add new",  click:() => {
    if(!this.app.getService("state").getState())
        this._jetPopup.showWindow();
}}
```

## Which Way of View Communication To Choose?

Each of these ways is good for different cases of view communication.

For example, it is better to use **URL parameters** to provide the information relevant for loading of the initial data into subview modules or for the initial state of Webix views.

**Events** are necessary for reacting to actions by multiple receivers. The direction of communication with events is _upward_ - from subviews to parents.

**Services** are relevant for storing a common state that can be used throughout the application or storing common methods that should be accessed by any Jet view. Communication with services _works in all directions_.

Also remember that you **can't use the same service for two instances of a view class**, e.g. if you create a file manager with two identical file views. For each instance of the class a new service is created. Use services if other ways aren't possible.

## Declaring and Calling Methods

One more effective way of connecting views is methods. Unlike events, methods can also return something useful. In one of the views, you can define a method, and in another view, you can call this method. 

Methods are preferable when you want to connect views with their subviews. Methods can only be used for view communication when you know that a view with the necessary method exists. A method is declared in the child view and called in its parent.

### Events vs Methods

Have a look at the example. Here is a view that has the *setMode()* method:

```js
// views/child.js
import {JetView} from "webix-jet";

export default class ChildView extends JetView{
    config(){
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

export default class ParentView extends JetView{
    config() {
        return {
            rows:[
                {
                    view:"button", value:"Set mode",
                    click:() => {
                        this.getSubView().setMode("readonly");
                    }
                }, 
                { $subview:"child" }	//load "views/child"
            ]
        };
    }
}
```

**this.getSubView()** refers to *ChildView* and calls the method. **getSubView()** can take a parameter with the name of a subview if there are several subviews, as you will see in [the "Referencing Views" chapter](referencing.md).

You can use methods for view communication in similar use-cases, but still events are more advisable. Now let's have a look at the example where methods are better then events.

### Methods vs Events

Suppose you want to create a file manager resembling Total Commander. The parent view will have two *file* subviews, each of them contains a list of files:

```js
// views/manager.js
...
config() { 
    return { 
        cols:[ 
            { name:"left", $subview:"fileview" }, 	// "views/files"
            { name:"right", $subview:"fileview" }	// "views/files"
        ]
}}
```

Here each subview has a name. *fileview* has the *loadFiles()* method. Let's tell the file manager, which paths to open in each file view:

```js
// views/manager.js
...
init() {
    this.getSubView("left").loadFiles("a");
    this.getSubView("right").loadFiles("b");
}
```

Both subviews are referenced with **getSubView(name)**.

<!-- footnotes -->
- - -
[1]:
To read more about how to reference apps and view classes, go to ["Referencing views"](../detailed/referencing.md).

[2]:
Event listeners created with **attachEvent()** have longer lifetimes than views that attached them. That's why, before you destroy the view, you have to detach event listeners to prevent memory leaks. Do not leave this task to the garbage collector.
