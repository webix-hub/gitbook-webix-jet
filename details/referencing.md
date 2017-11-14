# Referencing Views from Webix UI Events

In the new version of Webix Jet, there is a convenient reference to a Jet view from Webix UI events with the **this** pointer. Consider the following use-cases.

### 1. Reference to the View

Due to ES6 *arrow functions*, you can refer to a Jet view with **this** from click handlers of Webix views. This way is shorter and advisable. For example, **this.show** in the handler of the button refers to **Toolbar**: 

```js
// sources/views/toolbar.js
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: () => {
                this.show("details");
            }
    }]
};
export default Toolbar;
```

Another way to reference a Jet view is useful when you need to define a handler as *function*. In this case, **this** refers to the Webix view. Any Webix component created inside of view has the **$scope** property, which points to the instance of the view class. So if you want to change the URL from controls of the view, reference the view with **this.$scope**:

```js
/* sources/views/toolbar.js */
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: function() {
                this.$scope.show(this.getValue().toLowerCase());
            }
    }]
};
export default Toolbar;
```

This is one of the cases when arrow functions do not help to shorten the syntax. Have a look:

```js
//arrow function -- a very long way
{ view: "button", value:"Details",
    click: () => {
        this.show(this.getRoot().queryView({view:"button"}).getValue().toLowerCase());
    }
}
```

**getRoot()** and **queryView()** are discussed <a href="#below">below</a>.

### 2. Reference to the App

If you want to rebuild the app UI, you need to use *app.show()*. You can shorten the syntax with an **arrow function** and reference the app with **this.app**:

```js
// sources/views/toolbar.js
export default class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" },
                { view: "button", value:"Details",
                    click: () => {
                        this.app.show("/demo/details");
                        //or
                        //this.app.show("/demo" + this.getRoot().queryView({view:"button"}).getValue().toLowerCase());
                    }
            }]
        };
    }
}
```

**this.$scope.app** references the app that encloses the view if the handler is a simple function: 

```js
/* sources/views/toolbar.js */
export default class ToolbarView extends JetView {
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

### <span id="below">3. Reference to the Root UI Element of the View</span>

Suppose you have a view with a form and you want validate its input when users click **Submit**. To refer to the form itself and not the whole view, use **this.getRoot\(\)** in an arrow function. **getRoot()** references the root UI element returned by config, Webix Form in the code below: 

```js
// sources/views/form.js
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
// sources/views/form.js
{ view:"button", value:"save", 
    click: function() {
        if (this.$scope.getRoot().validate())
            this.$scope.show("details");
}}
```

### 4. Referencing Nested Views and Controls

You already know how to change the URL by controls. Now have a look how the state of controls can be changed by the URL. For example, if you have a toolbar with a segmented button that is used to switch between two views:

```js
/* sources/views/toolbar.js */
export default class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" }, 
                {
                    view: "segmented", options: ["Details", "Dash"],
                    click: function() {
                        this.$scope.app.show("/demo/" + this.getValue().toLowerCase());
                    }
            }]
        };
    }
}
```

This works fine, however, if you switch to **dash** and reload the page, the segmented button won't be turned to **dash**. In more complex apps this behavior might be confusing<sup><a href="#myfootnote1" id="origin">1</a></sup>. There is a way to solve this problem. You can reference a control inside a view and set its value in **init** of a class view that hosts the control. 

#### queryView()

You can use **queryView** to reference the control. With this method, you can look for control by any attribute, like *view*, *name*, etc.

```js
export default class ToolbarView extends JetView {
    config() {
        /* same config */
    }
    init(ui, url) {
        if (url.length)
            this.getRoot().queryView({view:"segmented"}).setValue(url[1].page);
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
                        var text = this.getRoot().queryView({ name: "email" })
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

#### Getting by IDs

You can also use **this.\$\$("localId")** to reference a control and set its value in **init** of the parent Jet view. **this.\$\$("localId")** can be used to reference nested views and controls by their global or local IDs.

```js
export default class ToolbarView extends JetView {
    config() {
        //...
        { view: "segmented", localId: "control", options: ["Details", "Dash"],
            click: function() {
                this.$scope.app.show("/demo/" + this.getValue().toLowerCase());
        }}
    }
    init(ui, url) {
        if (url.length)
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
        { view: "segmented", localId: "toolbar:segbtn", options: ["Details", "Dash"],
            click: function() {
                this.$scope.app.show("/demo/" + this.getValue().toLowerCase());
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