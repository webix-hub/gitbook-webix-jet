# Referencing Views

In the new version of Webix Jet, there is a convenient reference to a Jet view from Webix UI events with the **this** pointer. Consider the following use-cases.

## 1. Reference to the View

Due to ES6 _arrow functions_, you can refer to a Jet view with **this** from click handlers of Webix widgets. This way is shorter and advisable. For example, **this.show** in the handler of the button refers to **Toolbar**:

```javascript
// views/toolbar.js
import {JetView} from "webix-jet";
export default class Toolbar extends JetView {
    config(){
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" },
                { view: "button", value:"details",
                    click: () => {
                        this.show("details");
                    }
                }
            ]
        };
    }
};
```

Another way to reference a Jet view is useful when you need to define a handler as _function_. In this case, **this** refers to a Webix widget. Any Webix widget put inside of a view has the **$scope** property, which points to the Jet view. So if you want to change the URL from controls of the view, reference the view with **this.$scope** and call its **show** method:

```javascript
// views/toolbar.js
import {JetView} from "webix-jet";
export default class Toolbar extends JetView {
    config(){
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" },
                { view: "button", value:"details",
                    click: function() {
                        this.$scope.show(this.getValue());
                    }
                }
            ]
        };
    }
}
```

This is one of the cases when arrow functions do not help to shorten the syntax. Have a look at the same task done with an arrow function:

```javascript
// views/toolbar.js
...
{ view: "button", value:"details",
    click: () => {
        this.show(this.getRoot().queryView({view:"button"}).getValue());
    }
}
```

**getRoot\(\)** is discussed below.

## 2. Reference to the App

If you want to rebuild the app UI, you need to use _app.show\("/new/url"\)_. You can shorten the syntax with an **arrow function** and reference the app with **this.app**:

```javascript
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
                }
            ]
        };
    }
}
```

**this.$scope.app** references the app that encloses the view if the handler is a _simple function_:

```javascript
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

## 3. Reference to the Root UI Element of the View

Suppose you have a view with a form and you want to validate its input when users click **Submit**. To refer to the form itself and not the whole Jet view, use **this.getRoot\(\)** inside an arrow function. **getRoot\(\)** references the root UI element returned by **config**, which is Webix Form in the code below:

```javascript
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
                    }
                }
            ]
        };
    }
}
```

After the form is validated, the form will be replaced with the **details** view.

If the handler is not an arrow function, refer to the form or any other root UI element as **this.$scope.getRoot**:

```javascript
// views/form.js
{ view:"button", value:"save", 
    click: function() {
        if (this.$scope.getRoot().validate())
            this.$scope.show("details");
}}
```

## 4. Referencing Parent Views and Subviews

JetView class has two methods to reference subviews \(kids\) and parent views.

### 1. getParentView\(\)

**getParentView\(\)** is used to reference the parent view of a subview. For example, there is a Jet view with a Webix list and a form as a static subview:

```javascript
// views/listedit.js
import {JetView} from "webix-jet";

export default class Parent extends JetView{
    config(){
        return {
            cols:[
                { view:"list", select:true },
                { $subview:"form" }     //load "views/form"
            ]
        }
    }
    getSelected(){
        this.getRoot().queryView({view:"list"}).getSelectedItem();
    }
}
```

To get to the methods of Parent from the subview, you can use **this.getParentView\(\)**:

```javascript
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

**2. getSubView\("name"\)**

Use **getSubView\(\)** to get to the methods of subviews from a parent. Consider an example with a parent and two static subviews:

```javascript
// views/listedit.js
import {JetView} from "webix-jet";

export default class ListEditView extends JetView{
    config(){
        return {
            cols:[
                { $subview:"list", name:"list" },   //load "views/list"
                { $subview:"form", name:"form" }    //load "views/form"
            ]
        }
    }
    ready(){
        var list = this.getSubView("list").getRoot();
        this.getSubView("form").bindWith(list);
    }
}
```

When all the views are ready \(**ready\(\)** of the parent view is called after all its subviews are ready\), the form is bound to the list. Mind that the parent view must have the **bindWith\(\)** method that calls the **bind\(\)** method of a Webix form:

```javascript
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

## 5. Referencing Webix Widgets

You can reference a Webix widget inside a Jet view with **this.ui\(\)** by their global and local IDs. **this.$$\(\)** ignores widgets inside subviews [\[1\]](referencing-views.md#1).

### 1. Getting by Local IDS \(_localId_\)

You can add a local ID to a widget and then use **this.$$\("localId"\)** to reference it. Local IDs are better than global, because they isolate IDs inside the current Jet view.

Let's use the local ID to get to the segmented button and set its value in **init\(\)** of the parent Jet view:

```javascript
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

### 2. Getting by Global IDs

**this.$$\(id\)** can be used to reference nested views and controls by their _global IDs_. You should keep in mind that global IDs must be unique. The more developers are working on the project, the stronger the odds are that someone will give the same ID to some other widget.

You can give complex IDs, e.g. _"root\_view:control"_ or _"control:function"_. This will lessen the chances of non-unique IDs.

```javascript
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

If you use _this.$$\(id\)_ with a global ID, it's the same as _webix.$$\(id\)_.

## Footnotes:

#### \[1\]:

Starting with Webix Jet 1.5. If you want to look for widgets in the current view and all its subviews, use [this.getRoot\(\).queryView\(\)](https://docs.webix.com/api__ui.baseview_queryview.html).

