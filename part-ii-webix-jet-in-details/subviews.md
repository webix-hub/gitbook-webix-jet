# Subview Including

## 1. View Inclusion

You can include views into each other. Views included into other views are called **subviews**, and they can be either static or dynamic.

### 1. Static subviews

Static subviews are imported and placed into views directly.

```javascript
import Menu from "views/menu";
export default class TopView extends JetView {
    config(){
        return {
            rows:[
                { view:"button" },
                Menu
            ]
        };
    }
}
```

Subviews can also be included statically with the **$subview** keyword that points a class:

```javascript
import Menu from "views/menu";
export default class TopView extends JetView {
    config(){
        return {
            rows:[
                { view:"button" },
                { $subview:Menu }
            ]
        };
    }
}
```

You can include any number of static subviews.

### 2. Dynamic Subviews

Subviews which are resolved based on the URL segments are called **dynamic**. To create them, you need to put a placeholder into the UI with the help of **$subview:true**:

```javascript
// views/top.js
{
	cols:[
		{ view:"menu" },
		{ $subview:true }
	]
}
```

For example, here are two views created in a different ways:

- a class view that will be the parent

```js
// views/details.js
import {JetView} from "webix-jet";
export default class DetailsView extends JetView {
   	config(){
		return {
			rows: [
				{ template:"Details", type:"header" },
				{ $subview:true }
			]
		};
	}
}
```

- an object view that will be the subview

```javascript
// views/myview.js
export default MyView = {
    template:"MyView text"
}
```

To combine them, the app URL must be *"/details/myview"*, e.g. you can set the start URL in the app config to combine the views from the start:

```js
// myapp.js
import "./styles/app.css";
import { JetApp } from "webix-jet";

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            start : "/details/myview" // !
        };
        super({ ...defaults, ...config });
    }
}

webix.ready(() => new MyApp().render() );
```

### 3. Several dynamic subviews

Views can have several dynamic subviews. The corresponding URL segments are not shown in the address bar and are not included into the app URL. However, these subviews have app URLs of their own.

This feature is useful for big apps, the parts of which have complex structures of their own and work independently. E.g. a typical case is a file manager with two file lists that can be navigated independently. The view with several dynamic views is a "node", at which the app URL forks and the URL "branches" sprout:

```
            top/bigview
             |       |
/details/myview     /details/myview
```

The syntax in this case can be of two types:

1\. **$subview:"URL segment(s)"**

```javascript
// views/bigview.js
import {JetView} from "webix-jet";
export default class BigView extends JetView {
   	config(){
		return {
			cols:[
				{ $subview:"details" },			// one view
				{ $subview:"details/myview" }	// a view and its subview
			]
		};
	}
}
```

2\. **$subview:true, name:"subviewName" (named dynamic subviews)**

```js
// views/bigview.js
import {JetView} from "webix-jet";
export default class BigView extends JetView {
   	config(){
		return {
			cols: [
				{ $subview:true, name:"left" },
				{ $subview:true, name:"right" }
			]
		};
	}
}
```

To show such dynamic subviews, call **this.show()** with the second parameter - the name of the subview as the target:

```js
// views/bigview.js
init(){
	this.show("details/myview", { target:"left" });
	this.show("details/myview", { target:"right" });
}
```

A subview created in this way does not "know" anything about the URL changes of the parent and hence does not react to them. This means that the **urlChange()** of the subview is called only for URL changes of the its own branch of the app URL.

```js
// views/details.js
import {JetView} from "webix-jet";
export default class DetailsView extends JetView {
   	config(){
		return {
			rows: [
				{ template:"Details", type:"header" },
				{ $subview:true }
			]
		};
	}
	urlChange(){
		const url = this.getUrlString();
		// "details/myview"
	}
}
```

### View Inclusion into Popups and Windows

You can include a view into a **popup or a window**:

```javascript
// views/some.js
import WindowView from "views/window";
...
init(){
    this.win1 = this.ui(WindowView);
    //this.win1.showWin();
}
```

where _WindowView_ is a view class like the following:

```javascript
// views/window.js
import {JetView} from "webix-jet";
export default class WindowView extends JetView{
    config(){
        return { view:"window", body:{/* ...window content */} };
    }
    showWin(target){
        this.getRoot().show(target);
    }
}
```

For more details about popups and windows, [go to the "Popups and Windows" section](popups-and-windows.md).

## 2. App Inclusion

App is a part of the whole application that implements some scenario and is quite independent. It can be a subview as well. By including apps into other apps, you can create high-level applications. E.g. here are two views:

```javascript
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

```javascript
// views/app1.js
import {JetApp,EmptyRouter} from "webix-jet";

export const app1 = new JetApp({
    start: "/form",
    router: EmptyRouter //!
}); //no render!
```

Note that this app module isn't rendered. The second important thing is the choice of the router. As this is the inner level, it can't have a URL of its own. That's why _EmptyRouter_ is chosen. [Go to the "Routers" section](routers.md) for details.

Next, the app module is included into another view:

```javascript
// views/page.js
import {app1} from "app1";
import {toolbar} from "views/toolbar";

export default PageView = () => ({
    rows: [ toolbar, app1 ]
});
```

Finally, this view can also be put into another app:

```javascript
// app2.js
import {JetApp,HashRouter} from "webix-jet";

const app2 = new JetApp({
    start: "/page",
    router: HashRouter
}).render();
```

As a result, this is a two-level app.

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/viewapp.js).

Jet apps can also behave as Webix widgets, for details, check ["Big app development"](../part-iv-toolchain/big-app-development.md).
