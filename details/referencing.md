# <span id="contents">Referencing Views from Webix UI Events</span>

In the new version of Webix Jet, there is a convenient reference to a Jet view from Webix UI events with the **this** pointer. Consider the following use-cases.

- [Reference to the View](#ref_view)
- [Reference to the App](#ref_app)
- [Reference to the Root UI Element of the View](#below)
- [Referencing Parent Views and Subviews](#parent_sub)
- [Referencing Webix Widgets](#controls)

### [<span id="ref_view">1. Reference to the View &uarr;</span>](#contents)

Due to ES6 *arrow functions*, you can refer to a Jet view with **this** from click handlers of Webix widgets. This way is shorter and advisable. For example, **this.show** in the handler of the button refers to **Toolbar**: 

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

Another way to reference a Jet view is useful when you need to define a handler as *function*. In this case, **this** refers to a Webix widget. Any Webix widget put inside of a view has the **$scope** property, which points to the Jet view. So if you want to change the URL from controls of the view, reference the view with **this.$scope** and call its **show** method:

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

**getRoot()** is discussed <a href="#below">below</a>.

### [<span id="ref_app">2. Reference to the App &uarr;</span>](#contents)

If you want to rebuild the app UI, you need to use *app.show("/new/url")*. You can shorten the syntax with an **arrow function** and reference the app with **this.app**:

```js
// views/toolbar.js
import {JetView} from "webix-jet";

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
import {JetView} from "webix-jet";

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

### [<span id="below">3. Reference to the Root UI Element of the View &uarr;</span>](#contents)

Suppose you have a view with a form and you want to validate its input when users click **Submit**. To refer to the form itself and not the whole Jet view, use **this.getRoot\(\)** inside an arrow function. **getRoot()** references the root UI element returned by **config**, which is Webix Form in the code below: 

```js
// views/form.js
import {JetView} from "webix-jet";

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

### [<span id="parent_sub">4. Referencing Parent Views and Subviews &uarr;</span>](#contents)

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
                { $subview:"form" }
            ]
	    }
    }
    getSelected(){
        this.getRoot().queryView({view:"list"}).getSelectedItem();
    }
}
```

To get to the methods of Parent from the subview, you can use **this.getParentView()**:

```js
// views/form.js
import {JetView} from "webix-jet";

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

##### 2. getSubView("name")

Use **getSubView()** to get to the methods of subviews from a parent. Consider an example with a parent and two static subviews:

```js
// views/listedit.js
import {JetView} from "webix-jet";
import ChildList from "list";
import ChildForm from "form";

export default class ListEditView extends JetView{
	config(){
		return {
            cols:[
                { $subview:"list", name:"list" },
                { $subview:"form", name:"form" }
            ]
	    }
    }
	ready(){
        var list = this.getSubView("list").getRoot();
		this.getSubView("form").bindWith(list);
	}
}
```

When all the views are ready (**ready** of the parent view is called after all its subviews are ready), the form is bound to the list. Mind that the parent view must have the **bindWith** method that calls the **bind** method of a Webix form:

```js
// views/form.js
import {JetView} from "webix-jet";

export default class ChildForm extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" }
            ]
        };
    }
    bindWith(){
        this.getRoot().bind(widget);
    }
}
```

### [<span id="controls">5. Referencing Webix Widgets &uarr;</span>](#contents)

You can reference a Webix widget inside a Jet view in two ways. 

##### 1. Getting by Local IDS (*localId*) 

You can add a local ID to a widget and then use **this.\$\$("localId")** to reference it. Local IDs are better than global, because they isolate IDs inside the current Jet view.

Let's use the local ID to get to the segmented button and set its value in **init** of the parent Jet view: 

```js
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView {
    config() {
        return { view: "segmented", localId: "nav", options: ["details", "dash"],
            click: function() {
                this.$scope.app.show("/demo/" + this.getValue());
        }};
    }
    init(ui, url) {
        if (url.length > 1)
            this.$$("nav").setValue(url[1].page);
    }
}
```

##### 2. Getting by Global IDs

**this.\$\$(id)** can be used to reference nested views and controls by their *global or local IDs*. So you can add global IDs to widgets, but you should keep in mind that global IDs must be unique. The more developers are working on the project, the stronger the odds are that someone will give the same ID to some other widget.

One of the solutions is to give complex IDs, e.g. *"root_view:control"* or *"control:function"*. This will lessen the chances of non-unique IDs.

```js
// views/toolbar.js
...
{
    { view: "segmented", id: "segbtn:nav", options: ["details", "dash"],
        click: function() {
            this.$scope.app.show("/demo/" + this.getValue());
    }}
    ...
    thit.$$("segbtn:nav").setValue(url[1].page);
}
```

If you use *this.\$\$(id)* with a global ID, it's the same as *webix.\$\$(id)*.
