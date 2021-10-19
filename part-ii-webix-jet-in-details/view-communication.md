# View Communication

Views are separated, and there should be some means of communication between them.

## URL Parameters

Views can communicate through _URL parameters_ that are added after segments for the related views. You can pass parameters with the URL in the [show](jetview-api.md#this-show) method. One of the typical ways to use URL parameters is loading data into views as they are initialized (e.g. list-form pattern).

### Setting Parameters of Self

Use [this.setParam](../api/jetview-methods.md#this-setparam) to set the URL parameters of the current view:

```javascript
this.setParam("mode", "12", true); // some?mode=12
```

For example, let's change the mode parameter when a segment of Segmented is clicked:

```javascript
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

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

Work with URL parameters can be simplified with the [UrlParam plugin](plugins.md).

### Setting Parameters of Subviews

To set URL parameters for a view, pass them in [view.show](jetview-api.md#this-show) after the URL segment. In the example below, **show** will be called every time a record is selected in the table:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {getData} from "models/alldata";

export default class DataView extends JetView {
    config(){
        return {
            cols:[
                { view:"datatable", select:true, localId:"data", autoConfig:true },
                { $subview:true }
            ]
        };
    }
    init(){
        const table = this.$$("data");
        table.parse(getData());

        this.on(table, "onAfterSelect", id =>
            this.show(`form?id=${id}`)
        );

        table.select(table.getFirstId());
    }
}
```

`form` here is a Jet view file with a form, e.g.:

```javascript
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView {
    config(){
        return {
			view:"form", elements:[
				{ view:"text", name:"name" },
				{ view:"text", name:"email" }
			]
		};
    }
}
```

### Getting Parameters

To get the _id_ parameter in the form, call the [getParam](../api/jetview-methods.md#this-getparam) method. [urlChange](../api/jetview-handlers.md#urlchange) is the best lifetime handler for this since [init](../api/jetview-handlers.md#init) is called only once during the lifetime of a view.

```javascript
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
    urlChange(view){
        const id = this.getParam("id");
        if (id)
            view.setValues(getData(id));
    }
}
```

In this simple example, when a table row is selected, a form is filled with the related data. `getData` is a function that returns data.

[Live demo >>](https://snippet.webix.com/heuxnc38)

[More on GitHub](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

### Getting Parameters: Last Resort

[getParam](../api/jetview-methods.md#this-getparam) can get URL parameters of the current view and its direct parent. If you really need to access parameters of subviews, you can use the **url** parameter of [lifetime handlers](../api/jetview-handlers.md). However, keep in mind that accessing parameters of other views is risky, because you must be sure of the current app structure.

```javascript
// views/form.js
import {JetView} from "webix-jet";
import {getData} from "models/records";

export default class DataView extends JetView {
    config(){
        return {
            cols:[
                {
                    view:"datatable",
                    select:true,
                    localId:"data",
                    autoConfig:true
                },
                { $subview:true }
            ]
        };
    }
    urlChange(view, url){
        const subId = url[1].params.id;
    }
}
```

### Several URL Parameters

You can also pass several parameters to [view.show](jetview-api.md#this-show):

```javascript
this.show("form?name=Jack&email=some");
```

## Events

You can use the in-app event bus for view communication.

### Calling an Event

To call/trigger an event, call [app.callEvent](api/jetapp-methods.md#app-callevent). You can call the method by referencing the app with **this.app** from an _arrow function_ [\[1\]](view-communication.md#1):

```js
// views/subview.js
import { JetView } from "webix-jet";
import { some_data } from "models/somedata";
export default class SubView extends JetView {
	config(){
		return {
			view:"datatable",
            select:true,
            autoConfig:true,
			on:{
				onAfterSelect:row =>
                    this.app.callEvent("grid:record:select", [row])
			}
		}
	}
    init(view){
        view.parse(some_data);
    }
}
```

Calling events is necessary mostly for custom events. Inner Jet events and Webix events are called automatically \(e.g. the _onItemClick_ event of List\).

### Attaching an Event Listener

You can attach an event handler to the event bus in one view and trigger the event in another view.

#### Preferable Way: this.on

The best way to attach a listener is [this.on](jetview-api.md#this-on). The benefit of this way is that the event handler is automatically detached when the view that attached the event handler is destroyed. **this.on** can _handle_ app and Webix events.

```js
// views/parentview.js
import { JetView } from "webix-jet";
import { some_data } from "models/somedata";
export default class ParentView extends JetView {
	config(){
		return {
			rows:[
				{ view:"toolbar", elements:[ /* some toolbar controls*/ ] },
				{
					cols:[
						{
                            view:"list",
                            localId:"list",
                            template:"#name#",
                            data:some_data
                        },
						{ $subview:true } // here lives the datatable
					]
				}
			]
		}
	}
	init(){
		this.on(this.app, "grid:record:select", row => {
			this.$$("list").select(row.id);
		});
	}
}
```

#### One More Way: app.attachEvent

One more way to attach an event listener is to call [app.attachEvent](api/jetapp-methods.md#app-attachevent).

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

This event listener will stay as long as the app works.

## Services

A service is a way to share functionality. You can create a service connected to one of the views, and other views can communicate with the view through the service.

With ES6, the problem can also be solved with creating and requiring a module. If you create two apps that require the same module, there is only one instance of the shared code. Services are better, because a new instance of a service is created for each new instance of a Jet view class.

### Initializing Services

To set a service, call the [app.setService](api/jetapp-methods.md#app-setservice) method. **setService** requires two parameters:

* **service** - \(string\) the name of the service
* **obj** - \(object\) all methods that you want to call from other Jet views

For example, let's create a _masterTree_ view and let other views get the ID of a selected item in the master tree.

```javascript
// views/tree.js
import {JetView} from "webix-jet";

export default class treeView extends JetView{
    config(){
        return { view:"tree" };
    }
    init(view) {
        this.app.setService("masterTree", {
            getSelected : () => view.getSelectedId();
        });
    }
}
```

### Using Services

To get to the service and its methods, use the [app.getService](api/jetapp-methods.md#app-getservice) method. **getService** requires one parameter - the **name** of the service.

This is how you can get the ID of the selected node in the master tree and use it to fill a form with correct values:

```javascript
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
        const id = this.app.getService("masterTree").getSelected();
        view.setValues(getData(id));
    }
}
```

Apart from view communication, services can be used for loading and saving data. [You can read about it in the "Models" chapter](models.md#4-services-as-data-sources).

You can also use services to create app-level plugins.

```javascript
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

Such services should be included into the application in the start file \(e.g. _myapp.js_\) and enabled with **app.use**:

```javascript
//myapp.js
import {state} from "helpers/state.js";

const app = new JetApp({
    //...
});
app.render();
app.use(state);
```

Any view will be able to use the service as:

```javascript
{ view:"button", value:"Add new",  click:() => {
    if(!this.app.getService("state").getState())
        this._jetPopup.showWindow();
}}
```

## Which Way of View Communication To Choose?

Each of these ways is good for different cases of view communication. Let's summarize the typical uses of each one.

1\. It is better to use **URL parameters** to provide the information relevant for loading of the initial data into subview modules or for the initial state of Webix views.

2\. **Events** are necessary for reacting to actions by multiple receivers. A typical case is communication between subviews and parents, when a subview throws an event and the parent handles it.

3\. **Services** are relevant for storing a common state that can be used throughout the application or storing common methods that should be accessed by any Jet view. Communication with services works for all views and subviews regardless of their relations (whether they are a subview and a parent or not) and their life-cycles (whether views exist in the app at the same moment or not).

Also remember that you **can't use the same service for two instances of a view class**, e.g. if you create a file manager with two identical file views. For each instance of the class a new service is created. Use services if other ways aren't possible.

## View Class Methods

One more effective way of connecting views is methods. Unlike events, methods can also return something useful. In one of the views, you can define a method, and in another view, you can call this method.

Methods are preferable when you want to connect views with their subviews. Methods can only be used for view communication when you know that a view with the necessary method exists. A method is declared in the child view and called in its parent.

### Events vs Methods

Have a look at the example. Here is a view that has the _setMode_ method:

```javascript
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

And this is a parent view that will include _ChildView_ and call its method:

```javascript
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
                { $subview:"child" }    //load "views/child"
            ]
        };
    }
}
```

[this.getSubView](../api/jetview-methods.md#this-getsubview) refers to _ChildView_ and calls the method. **getSubView** can take a parameter with the name of a subview if there are several subviews, as you will see in [the "Referencing Views" chapter](referencing-views.md).

You can use methods for view communication in similar use-cases, but still events are more advisable. Now let's have a look at the example where methods are better then events.

### Methods vs Events

Suppose you want to create a file manager resembling Total Commander. The parent view will have two _file_ subviews, each of them contains a list of files:

```javascript
// views/manager.js
...
config() {
    return {
        cols:[
            { name:"left", $subview:"fileview" },     // "views/files"
            { name:"right", $subview:"fileview" }    // "views/files"
        ]
}}
```

Here each subview has a name. _fileview_ has the _loadFiles_ method. Let's tell the file manager, which paths to open in each file view:

```javascript
// views/manager.js
...
init() {
    this.getSubView("left").loadFiles("a");
    this.getSubView("right").loadFiles("b");
}
```

Both subviews are referenced with [this.getSubView](jetview-api.md#this-getsubview).

## Footnotes

#### \[1\]:

To read more about how to reference apps and view classes, go to ["Referencing views"](referencing-views.md).
