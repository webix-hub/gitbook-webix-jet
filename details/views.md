# JetView API

### this.use(plugin, config)

The method includes a plugin, for example,  this is how the **Status** plugin can be added:

```js
init(){
	this.use(plugins.Status, { 
		target: "app:status",
		ajax:true,
		expire: 5000
	});
}
```

For more details on plugins, check out the [related section](plugins.md).

### this.show("path")

This method is used to reload view modules according to the path specified as a parameter:

```js
/* sources/views/toolbar.js */
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: () => {
                this.show(this.getValue().toLowerCase());
            }
    }]
};
export default Toolbar;
```

For more details on view navigation, [read the dedicated article](../basic/navigation.md).

### this.ui(view)

**this.ui** call is equivalent to **webix.ui**. It creates a new instance of the view passed to it as a parameter. For example, you can create views inside popups or modal windows with **this.ui**. The good thing about this way is that it correctly destroys the window or popup when its parent view is destroyed. 

For example, you want to create a view with a list of orders and a form for editing records in the list. The form will be created inside a modal window. Here's the window:

```js
/* /views/orderform.js */
const orderform = {
	view:"window", modal:true,
	head:"Add new order",
	body:{
		view:"form", id:"order-form", elements:[
			{ view:"combo", name:"product", label:"Product", id:"order-product", 
            options:[
                { id:1, value:"Webix Chai"}, { id:2, value:"Webix Syrup"}, { id:3, value:"Webix Cajun Seasoning"}
            ]},
			{ view:"button", label:"Add", type:"form", align:"center", width:120, click:function(){
			    webix.$$("order-win").hide();
			}}
		]
	}
};
export default orderform;
```

Have a look at the parent view with a list of records:

```js
import data from "orders";
import orderform from "orderform";
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

The window is created in **init** of OrdersView and shown on a button click. And note again that there's no need to destroy the window manually.

For more details about popups and windows, [go to the related section](popups.md).

### this.getSubview(name)

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

### this.getParentView()

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

### this.on(app,"app:event:name",handler)

Use this method to attach events. This way of attatching an event is convenient, because it automatically detaches the event when the view that called it is destroyed.

```js
export default class FormView extends JetView{
    init(){
        this.on(this.app, "save:form", function(){
            this.show("aftersave");
        });
    }
}
```

See details in ["Events and Methods"](events.md).

### this.\$\$("controlID")

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