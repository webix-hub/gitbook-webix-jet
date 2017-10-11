## Referencing Views from Webix UI Events

In the new version of Webix Jet, there is a convenient reference to a view from Webix UI events with the **this** pointer. Consider the following use-cases.

#### 1.Reference to the view

Any Webix component created inside of view has the **$scope** property, which points to the instance of the view class. If you want to change the URL from controls of the view, reference the view with **this.$scope**:

```js
/* sources/views/toolbar.js */
export const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: function() {
                this.$scope.show(this.getValue())
            }
    }]
};
```

If you prefer a shorter syntax, let the handler be an arrow function. Then simply use **this** for view reference:

```js
/* sources/views/toolbar.js */
export const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: () => {
                this.show(this.getValue())
            }
    }]
};
```

#### 2.Reference to the app

**this.$scope.app** references the app that encloses the view. This is useful if you want to rebuild the app UI.

```js
/* sources/views/toolbar.js */
export class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" },
                { view: "button", value:"Details",
                    click: function () {
                        this.$scope.app.show("/demo/" + this.getValue().toLowerCase());
                    }
            }]
        };
    }
}
```

You can also shorten the syntax with an arrow function and reference the app with **this.app**:

```js
click: () => {
    this.app.show("/demo/" + this.getValue().toLowerCase());
}
```

#### 3.Reference to the root UI element of the view

Suppose you have a view with a form and you want to validate its input when users click **Submit**. To refer to the form itself and not the whole view, use **this.$scope.getRoot\(\)**. It references the root UI element returned by config. In an arrow function the reference is **this.getRoot\(\)**:

```js
/* sources/views/form.js */
export class FormView extends JetView{
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

After the form is validated, the form will be replaced with the **details** view. 

**4.Referencing nested views and controls**

You already know how to change the URL by controls. Now have a look how the state of controls can be changed by the URL. For example, if you have a toolbar with a segmented button that is used to switch between two views:

```js
/* sources/views/toolbar.js */
class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" }, 
                {
                    view: "segmented", localId: "control", options: ["Details", "Dash"],
                    click: function() {
                        this.$scope.app.show("/demo/" + this.getValue().toLowerCase());
                    }
            }]
        };
    }
}
```

This works fine, however, if you switch to **dash** and reload the page, the segmented button won't be turned to **dash**. In more complex apps this behavior might be confusing. To fix this, use **this.\$\$("localId")** to reference the button and set its value in **init**. **this.\$\$("localId")** can be used to reference nested views and controls by their global or local IDs.

```js
export default class ToolbarView extends JetView {
    config() {
        /* same config */
    }
    init(ui, url) {
        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
}
```

If you switch to **dash** and reload the page now, the state of the button will be restored correctly.

There's another way to reference a control inside a view. You can use **queryView** and pass a property with a value to the method as a parameter. Consider an example of simple form validation:

```js
/* views/form.js */
export default class FormView extends JetView{
    config(){
        return { 
            view:"form", elements:[
                { view:"text", name:"email",  label:"Email" },
                { view:"button", value:"save", 
                    click: () => {
                        var text = this.getRoot().queryView({ name: "email" })
                        if (text.getValue())
                            this.show("Details");
                    } 
                }
            ]
        };
    }
}
```

**queryView** will look for a view or a control with the *email* name. The same control can be found by its view type *text* or its label *Email*. The syntax is longer, but it works as fine as the search by its local ID.

<!-- from admin app orders.js
export default class OrdersView extends JetView{
	config(){
		return layout;
	}
	init(view){
		view.queryView({ view:"datatable" }).parse(data);
		this._form = this.ui(orderform);
	}
}
 -->