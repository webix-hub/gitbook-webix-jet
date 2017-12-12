# <span id="contents">JetView API</span>

JetView class has the following methods:

| Method                                | Use it to                             |
|---------------------------------------|---------------------------------------|
| [this.use(plugin, config)](#use)          | switch on a plugin                |
| [this.show("path")](#show)                | show a view or a subview          |
| [this.ui(view)](#ui)                      | create a popup or a window        |
| [this.on(app,"event:name",handler)](#on)  | attach an event                   |
| [this.getRoot()](#getroot)                | call methods of the Webix widget  |
| [this.getSubview(name)](#getsub)          | call the methods of a subview     |
| [this.getParentView()](#getpar)           | call methods of a parent view     |
| [this.\$\$("controlID")](#id)             | call methods of some Webix widget |

### [<span id="use">this.use(plugin, config) &uarr;</span>](#contents)

The method includes a plugin, for example, this is how the **Status** plugin can be added:

```js
// views/data.js
...
init(){
	this.use(plugins.Status, { 
		target: "app:status",
		ajax:true,
		expire: 5000
	});
}
```

For more details on plugins, check out the ["Plugins" section](plugins.md).

### [<span id="show">this.show("path") &uarr;</span>](#contents)

This method is used for in-app navigation. It loads view modules according to the path specified as a parameter:

```js
// views/toolbar.js */
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "button", value:"Details",
            click: () => {
                this.show("details");
            }
    }]
};
export default Toolbar;
```

**show** returns a *promise*. This is useful, when you plan to do something after a subview is rendered.

```js
// views/toolbar.js
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "button", value:"Details",
            click: () => {
                this.show("details").then(/* do something */);
            }
    }]
};
export default Toolbar;
```

##### Optional *target* Parameter

Subviews can have names. If you pass a configuration object with *target*, the view module will be shown as a subview with *name:target*:

```js
// views/big.js
...
{
    cols:[
        { $subview: true },
        { $subview: "right" },
    ]
}
...
this.show("small", { target:"right" })
```

where *small* is the name of the view module.

the above code is works the same as this:

```js
this.getSubView("right").show("small");
```
*getSubView* returns a subview by the name passed as a parameter.

For more details on view navigation, [read the "Navigation" article](../basic/navigation.md).

### [<span id="ui">this.ui(view) &uarr;</span>](#contents)

**this.ui** call is equivalent to **webix.ui**. It creates a new instance of the view passed to it as a parameter. For example, you can create views inside popups or modal windows with **this.ui**. The good thing about this way is that it correctly destroys the window or popup when its parent view is destroyed. 

#### *this.ui* for Windows and Popups

For example, you want to create a view with a list of orders and a form for editing records in the list. The form will be created inside a modal window. Here's the window:

```js
// views/orderform.js
const orderform = {
    view:"window",
    id:"order-win",
    modal:true,
	head:"Add new order",
	body:{
		view:"form", id:"order-form", elements:[
			{ view:"combo", name:"product", label:"Product", 
            options:[
                { id:1, value:"Webix Chai"}, { id:2, value:"Webix Syrup"}
            ]},
			{ view:"button", label:"Add", width:120, click:function(){
			    webix.$$("order-win").hide();
			}}
		]
	}
};
export default orderform;
```

Have a look at the parent view with a list of records:

```js
// views/orders.js
import data from "orders";
import orderform from "orderform";
import {JetView} from "webix-jet";

export default class OrdersView extends JetView{
	config(){
		return {
            rows:[
                { view: "button", type: "iconButton", icon: "plus", label: "Add order", width: 130, 
                click: function(){
                    this.$scope._form.show(this.$view);
                }},
                { view:"datatable" }
            ]
        }
	}
	init(view){
		view.queryView({ view:"datatable" }).parse(data);
		this._form = this.ui(orderform);
	}
}
```

The window is created in **init** of OrdersView and is shown on a button click. Note again that there's no need to destroy the window manually.

For more details about popups and windows, [go to the "popups and Windows" section](popups.md).

In the example above, a *Jet view* was passed to *this.ui*, so the return value of *this.ui* was also a Jet view. You can also pass *Webix widgets* or *Jet views wrapped inside a Webix layout*.

#### *this.ui* with a Webix widget config

*this.ui* returns a Webix UI object:

```js
class TopView extends JetView {
  ...
  init(){
  	this.win = this.ui({ template:"test" });
  }
}
```

#### *this.ui* with Jet views inside a Webix layout

*this.ui* returns a Webix UI object with Jet views inside:

```js
class SubView extends JetView {
  config(){
    return {template:"test"};
  }
};
class TopView extends JetView {
  ...
  init(){
  	this.win = this.ui({
          rows:[
            SubView
        ]
    });
  }
}
```

##### Optional *container* Parameter

**this.ui** has an optional parameter - *container*. If you provide it, the view will be rendered inside the container:

```js
var SubView = {
  template:"test"
};

class TopView extends JetView {
  config(){
    return {};
  }
  init(){
  	this.win = this.ui(SubView,{container:"here"});
  }
}
```

where *"here"* is the ID of a *div* element on the page. *container* can be an ID or an HTML element, e.g.:

```js
this.win = this.ui(SubView,{container:document.getElementById("here")});
```

### [<span id="on">this.on(app,"app:event:name",handler) &uarr;</span>](#contents)

Use this method to attach events. This way of attatching an event is convenient, because it automatically detaches the event when the view that called it is destroyed. This helps to avoid memory leaks that may happen, especially in older browsers.

```js
export default class FormView extends JetView{
    init(){
        this.on(this.app, "save:form", function(){
            this.show("aftersave");
        });
    }
}
```

For more details on attaching and calling events, read the ["Events and Methods" section](events.md).

### [<span id="getroot">this.getRoot() &uarr;</span>](#contents)

Use this method to return the Webix widget inside a Jet class view and to call methods of a Webix widget.

```js
// views/form.js
export default class FormView extends JetView{
    config(){
        return { 
            view:"form", elements:[
                { view:"text", name:"email", required:true, label:"Email" },
                { view:"button", value:"save", 
                    click: () => {
                        if (this.getRoot().validate())
                            this.show("details");
                }}
        ]};
    }
}
```

For more details on referencing views, [read the "Referencing" section](referencing.md).

### [<span id="getsub">this.getSubview(name) &uarr;</span>](#contents)

Use this method if you want to get to the methods of a subview. It looks for a subview by its name. So you must set the name. You can do it like this:

```js
// views/listedit.js

import {JetView} from "webix-jet";
import ChildList from "list";
import ChildForm from "form";

export default class ListEditView extends JetView{
	config(){
		return {
            cols:[
                { $subview:list, name:"list" },
                { $subview:form, name:"form" }
            ]
	    }
	}
}
```

After you set the name to a subview, you can refer to it with **this.getSubView(name)** from the methods of the parent:

```js
// views/listedit.js

import {JetView} from "webix-jet";
import ChildList from "list";
import ChildForm from "form";

export default class ListEditView extends JetView{
	...
	ready(){
        var list = this.getSubview("list").getRoot();
		this.getSubview("form").bind(list);
	}
}
```

For more details on referencing views, [read the "Referencing" section](referencing.md).

### [<span id="getpar">this.getParentView() &uarr;</span>](#contents)

Use this method to get to the methods of the parent view.

```js
// views/form.js
export default class Child extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" }
            ]
        };
    }
    init(view){
        var item = this.getParentView().getSelected();
        view.setValues(item);
    }
}
```

The child refers to its parent view with **this.getParentView** and calls its **getSelected** method.

For more details, [read the "Referencing" section](referencing.md).

See details in ["Events and Methods"](events.md).

### [<span id="id">this.\$\$("controlID") &uarr;</span>](#contents)

Use **this.$$** to look for nested views by their IDs.

```js
// views/toolbar.js
export default class ToolbarView extends JetView {
    config() {
        //...
        { view: "segmented", localId: "control", options: ["details", "dash"],
            click: function() {
                this.$scope.app.show("/demo/" + this.getValue());
        }}
    }
    init(ui, url) {
        if (url.length > 1)
            this.$$("control").setValue(url[1].page);
    }
}
```

For details, [read the "Referencing" section](referencing.md).