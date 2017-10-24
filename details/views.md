# JetView API

### this.use

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