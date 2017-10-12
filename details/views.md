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

### this.ui

**this.ui** call is equivalent to **webix.ui**. It creats a new instance of the view passed to it as a parameter. For example, you want to create a view with a list of orders and a form for editing records in the list. Here's the view with a form:

```js
const ui = {
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
export default ui;
```

The form is created inside a modal window. Have a look at the view with a list of records and a button that will show the form:

```js
import data from "orders"
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

