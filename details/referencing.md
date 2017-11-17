# Referencing Views from Webix UI Events

In the new version of Webix Jet, there is a convenient reference to a Jet view from Webix UI events with the **this** pointer. Consider the following use-cases.

### 1. Reference to the View

Due to ES6 *arrow functions*, you can refer to a Jet view with **this** from click handlers of Webix views. This way is shorter and advisable. For example, **this.show** in the handler of the button refers to **Toolbar**: 

```js
// views/toolbar.js

const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"details",
            click: () => {
                this.show("details");
            }
    }]
};
export default Toolbar;
```

Another way to reference a Jet view is useful when you need to define a handler as *function*. In this case, **this** refers to a Webix view. Any Webix component created inside of a view has the **$scope** property, which points to the Jet view. So if you want to change the URL from controls of the view, reference the view with **this.$scope** and call its **show** method:

```js
// views/toolbar.js

const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"details",
            click: function() {
                this.$scope.show(this.getValue());
            }
    }]
};
export default Toolbar;
```

This is one of the cases when arrow functions do not help to shorten the syntax. Have a look at the same task done with an arrow function:

```js
// views/toolbar.js

{ view: "button", value:"details",
    click: () => {
        this.show(this.getRoot().queryView({view:"button"}).getValue());
    }
}
```

**getRoot()** and **queryView()** are discussed <a href="#below">below</a>.

### 2. Reference to the App

If you want to rebuild the app UI, you need to use *app.show("/new/url")*. You can shorten the syntax with an **arrow function** and reference the app with **this.app**:

```js
// views/toolbar.js

export default class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" },
                { view: "button", value:"details",
                    click: () => {
                        this.app.show("/demo/details");
                        //or
                        //this.app.show("/demo/" + this.getRoot().queryView({view:"button"}).getValue());
                    }
            }]
        };
    }
}
```

**this.$scope.app** references the app that encloses the view if the handler is a *simple function*: 

```js
// views/toolbar.js
export default class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" },
                { view: "button", value:"details",
                    click: function () {
                        this.$scope.app.show("/demo/"+this.getValue());
                    }
            }]
        };
    }
}
```

### <span id="below">3. Reference to the Root UI Element of the View</span>

Suppose you have a view with a form and you want to validate its input when users click **Submit**. To refer to the form itself and not the whole Jet view, use **this.getRoot\(\)** inside an arrow function. **getRoot()** references the root UI element returned by **config**, which is Webix Form in the code below: 

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

After the form is validated, the form will be replaced with the **details** view.

If the handler is not an arrow function, refer to the form or any other root UI element as **this.$scope.getRoot**:

```js
// views/form.js
{ view:"button", value:"save", 
    click: function() {
        if (this.$scope.getRoot().validate())
            this.$scope.show("details");
}}
```

### 4. Referencing Parent Views and Subviews

JetView class has two methods to reference subviews (kids) and parent views.

##### 1. getParentView()

**getParentView()** is used to reference the parent view of a subview. For example, there is a Jet view with a Webix list and a form as a static subview:

```js
// views/listedit.js

import {JetView} from "webix-jet";
import form from "form";

export default class Parent extends JetView{
	config(){
		return {
            cols:[
                { view:"list", select:true },
                { $subview:form }
            ]
	    }
    }
    getSelected(){
        this.getRoot.getSelectedItem();
    }
}
```

To get to the methods of Parent from the subview, you can use **this.getParentView()**:

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

When the parent and the subview are rendered, the form in the subview gets the name of the selected item in the list from the parent.

##### 2. getSubview("name")

Use **getSubview()** to get to the methods of subviews from a parent. Consider an example with a parent and two static subviews:

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
	ready(){
        var list = this.getSubview("list").getRoot();
		this.getSubview("form").bind(list);
	}
}
```

When all the views are ready (**ready** of the parent view is called after all its subviews are ready), the form is bound to the list. Mind that the parent view must have the **bind** method that calls the **bind** method of a Webix form:

```js
// views/form.js
export default class ChildForm extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" }
            ]
        };
    }
    bind(){
        this.getRoot().bind(widget);
    }
}
```

### 5. Referencing Webix Views and Controls

You already know how to change the URL by controls. Now have a look how the state of controls can be changed by the URL. For example, if you have a toolbar with a segmented button that is used to switch between two views:

```js
// views/toolbar.js 
export default class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" }, 
                {
                    view: "segmented", options: ["details", "dash"],
                    click: function() {
                        this.$scope.show(this.getValue());
                    }
            }]
        };
    }
}
```

This works fine, however, if you switch to **dash** and reload the page, the segmented button won't be turned to **dash**. In more complex apps this behavior might be confusing<sup><a href="#myfootnote1" id="origin">1</a></sup>. There is a way to solve this problem. You can reference a control inside a view and set its value in **init** of a class view that hosts the control. 

##### 1. queryView()

You can use **queryView** to reference the control. With this method, you can look for control by any attribute, like *view*, *name*, etc.

```js
// views/toolbar.js
export default class ToolbarView extends JetView {
    config() {
        /* same config with segmented button */
    }
    init(view, url) {
        if (url.length > 1)
            view.queryView({view:"segmented"}).setValue(url[1].page);
    }
}
```

If you switch to **dash** and reload the page now, the state of the button will be restored correctly.

**queryView()** is also often used for form validation. Consider also an example of simple form validation:

```js
/* views/form.js */
export default class FormView extends JetView{
    config(){
        return { 
            view:"form", elements:[
                { view:"text", name:"email",  label:"Email" },
                { view:"button", value:"save", 
                    click: () => {
                        var text = this.getRoot().queryView({ name: "email" });
                        if (text.getValue())
                            this.show("details");
                    } 
                }
            ]
        };
    }
}
```

**queryView** will look for a view or a control with the *email* name inside the form. The same control can be found by its view type *text* or its label *Email*. 

##### 2. Getting by IDs

You can also use **this.\$\$("localId")** to reference a control and set its value in **init** of the parent Jet view. **this.\$\$("localId")** can be used to reference nested views and controls by their global or local IDs.

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

#### Which way to reference controls and Webix views is better?

**IDs** must be unique, and the more developers are working on the project, the stronger odds are that someone will give the same IDs to another view.

One of the solutions is to give complex IDs, e.g. *"root:control"*:

```js
{
    view: "toolbar",
    elements: [
        { view: "segmented", localId: "toolbar:segbtn", options: ["details", "dash"],
            click: function() {
                this.$scope.app.show("/demo/" + this.getValue());
        }}
    ]
}
```

This will lessen the chances of non-unique IDs.

**queryView** is another solution to this problem. The syntax is longer, but it works as fine as the search by its local ID and lets you avoid IDs at all.

<!-- footnotes -->
- - -
<a id="myfootnote1" href="#origin">1</a>:
Actually, this task can be solved with the Menu plugin. You can [read about it in the dedicated section](plugins.md).