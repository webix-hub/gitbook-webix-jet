# <span id="contents">Views and SubViews</span>

- Views
    - [Simple Views](#simple)
    - [Object "Factory Pattern"](#factory)
    - [Class Views](#class_views)
- [SubView Including](#subviews)
    - [View Inclusion](#view_subview)
    - [App Inclusion](#app_subview)


After reading the "Basics" chapter of this guide, you are familiar with the concept of a _view_. Now it's time to find out all the ways of creating views. You can create views in three ways.

## [<span id="simple">1. Simple Views &uarr;</span>](#contents)

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

or 

```js
const list = {
    view:"list"
};
export default list;
```

## [<span id="factory">2. Object "Factory Pattern" &uarr;</span>](#contents)

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

## [<span id="class_views">3. Class Views &uarr;</span>](#contents)

Views can be defined as JS6 classes.

### Advantages of Classes

- Views defined as classes are **dynamic** and each new instance can be changed when it's created.

- View classes have **init** and other **methods** that can be redefined by users. 

- You can also define **custom methods** and **local variables**.

- All instances have their individual **inner states**. E.g. if you use the same Toolbar class to add identical toolbars at the top and at the bottom, there are two instances of a Toolbar class and the toolbars will behave independently.

- Classes have the **this** pointer that references the view inside methods and handlers.

- You can **extend** class views. With JS6 classes, inheritance is closer to classic OOP and the syntax is nicer. Inheritance can help you reuse old components for creating slightly different ones. For example, if you already have a toolbar and want to create a similar one, but with one additional button, define a new class and inherit from the old toolbar.

### <span id="methods">JetView Methods</span>

View classes inherit from **JetView**. Webix UI lifetime event handlers are implemented through class methods. Here are the methods that you can redefine while defining your class views:

- [config()](#config)
- [init()](#init)
- [urlChange()](#urlchange)
- [ready()](#ready)
- [destroy()](#destroy)

#### [<span id="config">config() &uarr; </span>](#methods)

This method returns the initial UI configuration of a view. Have a look at a toolbar:

```js
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
    config(){
        return {
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", options:["details", "dash"] }
            ]
        };
    }
}
```

**config** of *ToolbarView* class returns a simple Webix toolbar.

#### [<span id="init">init\(view, url\) &uarr;</span>](#methods)

The method is called only once for every instance of a view class when the view is rendered. It can be used to change the initial UI configuration of a view returned by **config**. For instance, the above-defined toolbar will be always rendered with the first segment of the button active. You can change the control state in **init**. Let's link it to the URL:

```js
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", options:["details", "dash"] }
            ]
        };
    }
    init(view, url){
        if (url.length > 1)
            view.queryView({view:"segmented"}).setValue(url[1].page);
    }
}
```

The **init** method receives two **parameters**:

##### 1. **view** - the view UI

The segmented button is referenced by **view.queryView()**. **view** is received by **init** as one of the two parameters and references a Webix view inside the class instance. **queryView** looks for a view (a segmented button in this case) by its attributes. For more details on referencing nested views, [read the "Referencing views" section](referencing.md).

##### 2. **url** - the app URL as an array

Each array element is an object that contains:

- **page** - the name of the URL element
- **params** - parameters that you can pass with the URL
- **index** - the index of the URL element (beginning from 1)

So when **setValue** in the code above was passed *url[1].page*, it received the name of the current subview (*details* or *dash*).

*url.length > 1* checks that a subview is present in the URL.

#### [<span id="urlchange">urlChange\(view,url\) &uarr;</span>](#methods)

This method is called every time the URL is changed. It reacts to the change in the URL after **!\#**<sup><a href="#footnote1" id="origin">1</a></sup>. **urlChange** is only called for the view that is rendered and for its parent. Consider the following example. The initial URL is:

```
/layout/demo/details
```

If you change it to:

```
/layout/demo/preview
```

**urlChange** will be called for **preview** and **demo**.

The **urlChange** method can be used to restore the state of the view according to the URL, e.g. to highlight the right controls.

Let's expand the previous example with a toolbar and add a click handler to the segmented button that will change the URL:

```js
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", options:["details", "dash"], click:function(){
                    this.$scope.show(this.getValue());
                }}
            ]
        };
    }
}
```

As the click handler is a simple function, you must refer to the Jet view class instance as **this.$scope** to call its **show** method. **this** in simple *function* handler refers to the Webix control, the segmented button in this case. You can read more on ["Referencing views"](referencing.md).

Here's how you can select the right segment of the button in **urlChange**:

```js
// views/toolbar.js
    urlChange(view, url){
        if (url.length > 1)
            view.queryView({view:"segmented"}).setValue(url[1].page);
    }
```

The **urlChange** method receives the same two **parameters** as **init**:

- **view** - the Webix view inside the Jet view class
- **url** - the URL as an array of URL elements

#### [<span id="ready">ready(view,url) &uarr;</span>](#methods)

**ready** is called when the current view and all its subviews have been rendered. For instance, if the URL is changed to *a/b*, the order in which view class methods are called is the following:

```
config a
init a
	config b
	init b
	urlChange b
	ready b
urlChange a
ready a
```

Here's how you can use **ready**. There are two simple views, a list and a form for editing the list:

```js
// views/list.js
const list = {
    view: "list",
    select: true,
    template: "#value#",
    data: [{value:"one"},{value:"two"}]
};

// views/form.js
const form = {
	view: "form",
	elements:[
		{view: "text", name: "value", label: "Value"},
		{view: "button", value: "Save", width: 90}
	]
};
```

Let's include these views into one module and bind the list to the form:

```js
// views/listedit.js
import {JetView} from "webix-jet";
import list from "list";
import form from "form";

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
		this.getSubview("form").bind(this.getSubview("list"));
	}
}
```

In the example, the form will be bound to the list only when both the list and the form are rendered.

Note that *subviews* can have **names**. If you give a name to a subview, you can reference it as **this.getSubview("name")**, where **this** is the instance of the class view that includes this subview. You can read more on ["Referencing views"](referencing.md).

**ready** receives same two **parameters**:

- **view** - the Webix view inside the Jet view class
- **url** - the URL as an array of URL elements

#### [<span id="destroy">destroy() &uarr;</span>](#methods)

**destroy** is called only once for each class instance when the view is destroyed. The view is destroyed when the corresponding URL element is no longer present in the URL.

```js
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", options:["details", "dash"], click:function(){
                    this.$scope.show(this.getValue())
                }}
            ]
        };
    }
    destroy(){
        webix.message("I'm dying!");
    }
}
```

You can use **destroy** to detach events that were attached by this view with **app.attachEvent**. Events attached by **attachEvent** aren't destroyed automatically.

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    init(){
        this.app.attachEvent("save:form", function(){
            this.show("aftersave");
        });
    }
    destroy(){
        this.app.detachEvent("save:form");
    }
}
```

## &#x2753; Which Way to Define Views is Better

If you still doubt which way to choose for defining views, here's a summary.

All ways provide nearly the same result.

When you are using the **"class"** approach, you can define UI config and *init|urlChange|ready|destroy* handlers.

When you are using the **"factory function"** approach, you can define a dynamic UI config without lifetime handlers.

When you are defining views as **const** *(simple view objects)*, you can define UI config only.

So if you are choosing between **classes** and **const**, it is flexibility VS brevity.

If you are not sure which one to use, use classes. A class with the **config** method works exactly the same as the **const** declaration.

# [<span id="subviews">Subview Including &uarr;</span>](#contents)

Apart from direct inclusion [described in the second chapter](../basic/views.md), there are two more ways of including subviews. Let's recap all the possible ways in short:

##### 1. Direct Static Including

- plain including:

```js
import child from "child";
...
{
    rows:[
        { view:"button" },
        child
    ]
}
```

- one view including with $subview:view:

```js
import child from "child";
...
{
    rows:[
        { view:"button" },
        { $subview:child }
    ]
}
```

- including a hierarchy of views with $subview:"top/some":

```js
import child from "child";
import grandchild from "grandchild";

...
{
    rows:[
        { view:"button" },
        { $subview:"child/grandchild" }
    ]
}
```

##### 2. Dynamic Including
    
- { $subview:true }

## Subview Inclusion in Details

You can include views and apps into other views.

### [<span id="view_subview">1. View Inclusion &uarr;</span>](#contents)

For example, here are three views created in different ways:

- a class view

```js
// views/myview.js

export default class MyView extends JetView {
    config() => { template:"MyView text" };
}
```

- a simple view object

```js
// views/details.js

export default Details = { 
    cols: [
        { template:"Details text" },
        { $subview:true } 
    ]    
}
```

- a view returned by a factory

```js
// views/form.js

export default Form = () => {
    view:"form", elements:[
        { view:"text", name:"email", required:true, label:"Email" },
        { view:"button", value:"save", click:() => this.show("details") }
    ]
}
```

**{ $subview:true }** is a placeholder for a dynamically included subview.

Let's group them into a bigger view:

```js
// views/bigview.js
import myview from "myview";
import details from "details";
import form from "form";

export default BigView = {
    rows:[
        myview,
        { $subview:"/details/form" }
    ]
}
```

Mind that all these views could be put in any order you want and it doesn't depend whether they are classes or objects, e.g.:

```js
// views/bigview.js

export default BigView = {
    rows:[
        details,
        { $subview:"/myview/form" }
    ]
}
```

#### View Inclusion into Popups and Windows

You can also include a view into a **popup or a window**:

```js
// views/some.js
...
init(){
    this.win1 = this.ui(WindowView);
    //this.win1.show();
}
```

where *WindowView* is a view class like the following:

```js
// views/window.js
import {JetView} from "webix-jet";

export default class WindowView extends JetView{
  config(){
      return { view:"window", body:{} };
  }
  show(target){
      this.getRoot().show(target);
  }
}
```

For more details about popups and windows, [go to the "Popups and Windows" section](popups.md).

### [<span id="app_subview">2. App Inclusion &uarr;</span>](#contents)

App is a part of the whole application that implements some scenario and is quite independent. It can be a subview as well. By including apps into other apps, you can create high-level applications. E.g. here are two views:

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView {
    config() {
        return {
            view: "form",
            elements: [
                { view: "text", name: "email", required: true, label: "Email" },
                { view: "button", value: "save", click: () => this.show("details") }
            ]
        };
    }
}

// view/details.js
export default DetailsView = () => ({
    template: "Data saved"
});
```

Let's group these views into an app module:

```js
// views/app1.js
import {JetApp,EmptyRouter} from "webix-jet";

export var app1 = new JetApp({
    start: "/form",
    router: EmptyRouter //!
}); //no render!
```

Note that this app module isn't rendered. The second important thing is the choice of the router. As this is the inner level, it can't have URL of its own. That's why *EmptyRouter* is chosen. [Go to the "Routers" section](routers.md) for details. 

Next, the app module is included into another view:

```js
// views/page.js
import {app1} from "app1";
import {toolbar} from "toolbar";

export default PageView = () => ({
    rows: [ toolbar, app1 ]
});
```

Finally, the view can also be put into another app:

```js
// app2.js
import {JetApp,HashRouter} from "webix-jet";

var app2 = new JetApp({
    start: "/page",
    router: HashRouter
}).render();
```

As a result, this is a two-level app.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/b686944b383745070fc977aa9123f01a36ce2b3c/viewapp.js).

<!-- footnotes -->
- - -
<a href="#origin" id="footnote1">1</a>:
This is true if you use *HashRouter*. There's no hashbang with other routers, but this still works for *URL* and *Store* routers. The URL isn't stored only for *EmptyRouter*. For details, [go to the "Routers" section](routers.md).