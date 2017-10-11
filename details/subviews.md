## Views

After reading the first chapter of this guide, you are familiar with the concept of a _view_. Now it's time to find out all the ways of creating views. You can create views in three ways.

## 1. Simple Views

Views can be created as pure objects.

### Advantage 

- This is a simple way to create a view.

### Disadvantages
 
- Simple views are static and are included as they are.
- Simple views have no **init** and other methods that classes have.

Here's a simple view with a list:

```js
/* views/list.js */
export default {
	view:"list"
}
```

## 2. Object "Factory Pattern"

View objects can also be returned by a factory function.

### Advantages

- Such views are still simple.
- Such views are dynamic.

### Disadvantages

- Such views have no **init** or other methods that classes have.

Here's a list view returned by a factory:

```js
/* views/details.js */
export default () => {
	var data = [];
	for (var i=0; i<10; i++) data.push({ value:i });

	return {
		view:"list", options:data
	}
}
```

## 3. Class Views

Views can be defined as JS6 classes.

### Advantages of classes

- Views defined as classes are **dynamic** and each new instance can be changed when it's created.

- View classes have **init** and other **methods** that can be redefined by users.

- All instances have their individual **inner states**. E.g. if you use the same Toolbar class to add identical toolbars at the top and at the bottom, there are two instances of a Toolbar class and the toolbars will behave independently.

- Classes have the **this** pointer that references the view inside methods and handlers.

- With JS6 classes, **inheritance** is closer to classic OOP and the syntax is nicer. Inheritance can help you reuse old components for creating slightly different ones. For example, if you already have a toolbar and want to create a similar one, but with one additional button, define a new class and inherit from the old toolbar.

### JetView Methods

View classes inherit from **JetView**. Webix UI events are implemented through class methods. Here are the methods that you can redefine while defining your class views:

#### config

This method returns the initial UI configuration of a view. Have a look at a toolbar:

```js
/* views/toolbar.js */
class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
}
```

#### init\(view, url\)

The method is called only once for every instance of a view class when the view is rendered. It can be used to change the initial UI configuration of a view. For instance, the above-defined toolbar will be always rendered with the first segment active regardless of the URL. You can link the control state to the URL:

```js
export default class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
    init(view, url){
        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
}
```

The method receives two parameters:

* the view UI
* the URL

#### urlChange\(view,url\)

This method is called every time the URL is changed. It reacts to the change in the URL after **!\#**[^1]. **urlChange** is only called for the view that is rendered and for its parent. Consider the following example. The initial URL is:

```
/Layout/Demo/Details
```

If you change it to

```
/Layout/Demo/Preview
```

**urlChange** will be called for **Preview** and **Demo**. [Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/02_life_stages.html).

The **urlChange** method can be used to restore the state of the view according to the URL, e.g to highlight the right menu item.

```js
class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
    init(view, url){
        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
    urlChange(view, url){
        if (url.length > 1)
            this.$$("control").setValue(url[1].page);
    }
}
```

The method receives two parameters:

* the view UI
* the URL

#### ready(view,url)

**ready** is called when the current view and all its subviews are rendered. For instance, if the URL is changed to *a/b*, the order in which view class methods are called is the following:

```
config a
init a
urlChange a
	config b
	init b
	urlChange b
	ready b
ready a
```

Here's how you can use **ready**. There are two simple views, a list and a form for editing the list:

```js
/* views/list.js */
const list = {
    view: "list",
    id: "list",
    select: true,
    template: "#value#",
    data: [{value:"one"},{value:"two"}]
};

/* views/form.js */
const form = {
	view: "form",
	id: "form",
	elements:[
		{view: "text", name: "value", label: "Value"},
		{view: "button", value: "Save", width: 90}
	]
};
```

Let's include these views into one module and bind the list to the form:

```js
/* sources/views/listedit.js*/
import {JetView} from "webix-jet";
import list from "list";
import form from "form";

export default class ListEditView extends JetView{
	config(){
		return {
            cols:[
                list, form
            ]
	    }
    }
	ready(){
		webix.$$("form").bind(webix.$$("list"));
	}
}
```

In the example, the form will be bound to the list only when both the list and the form are rendered.

The method also receives two parameters:

* the view UI
* the URL

#### destroy

**destroy** is called only once for each instance when the view is destroyed. The view is destroyed when the corresponding URL element is no longer present in the URL. **destroy** also can be used to destroy temporary objects like popups and other child components to prevent memory leaks.

```js
class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
    init(view, url){
        var popup = webix.ui({
            view:"popup", 
            body:"Toolbar is created"
        });

        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
    urlChange(view, url){
        if (url.length > 1)
            this.$$("control").setValue(url[1].page);
    }
    destroy(){
        popup.destructor();
    }
}
```

[Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/02_life_stages.html) to see the order of the life stages of each view.

## Subview Including

Apart from direct inclusion, there are two more ways of creating subviews.

### 1. View Inclusion

You can inject views into other views. For example, here are three views created in different ways:

- a class view

```js
/* views/myview.js */
export class MyView extends JetView {
    config() => { template:"MyView text" };
}
```

- a simple view object

```js
/* views/details.js */
export default Details = { 
    cols: [
        { template:"Details text" },
        { $subview:true } 
    ]    
}
```

- a view returned by a factory

```js
/* views/form.js */
export default Form = () => {
    view:"form", elements:[
        { view:"text", name:"email", required:true, label:"Email" },
        { view:"button", value:"save", click:() => this.show("Details") }
    ]
}
```

Let's group them into a bigger view:

```js
/* views/bigview.js */
import {MyView} from "myview"
import {details} from "details"
import {form} from "form"

export default BigView = {
    rows:[
        MyView,
        { $subview:"/details/form" }
    ]
}
```

### 2. App Inclusion

App is a part of the whole application that implements some scenario and is quite independent. It can be a subview as well. By including apps into other apps, you can create high-level applications.

```js
/* views/form.js */
export defaulst class FormView extends JetView {
    config() {
        return {
            view: "form",
            elements: [
                { view: "text", name: "email", required: true, label: "Email" },
                { view: "button", value: "save", click: () => this.show("Details") }
            ]
        };
    }
}
/* view/details.js */
export default DetailsView = () => ({
    template: "Data saved"
});
```

Let's group these views into an app module:

```js
/* views/app1.js */
import {FormView} from "form"
import {DetailsView} from "details"
export var app1 = new JetApp({
    start: "/Form",
    router: JetApp.routers.EmptyRouter,
    views: {
        "Form": FormView,
        "Details": DetailsView
    }
}); //no render
```

Note that this app module isn't rendered. The second important thing is the choice of the router. As this is the inner level, it can't have URL of its own. That's why *EmptyRouter* is chosen. [Go to the section on routers](routers.md) for details. 

Next, the app module is included into another view:

```js
/* views/page.js */
import {app1} from "app1"
export default PageView = () => ({
    rows: [ Toolbar, app1 ]
});
```

Finally, the view can also be put into another app:

```js
/* app2.js */
import {PageView} from "page"
var app2 = new JetApp({
    start: "/Page",
    router: JetApp.routers.HashRouter,
    views: {
        "Page": PageView
    }
}).render();
```

As a result, this is a two-level app. [Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/06_highlevel.html).

<!-- footnotes -->
[^1]:
This is true if you use *HashRouter*. There's no hashbang with other routers, but this still works for *URL* and *Store* routers. The URL isn't stored only for EmptyRouter. For details, [go to the dedicated section](routers.md).