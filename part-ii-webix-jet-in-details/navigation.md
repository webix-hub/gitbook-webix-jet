# Navigation

There are several ways to navigate through an app with Webix Jet.

## 1. Jet Links

Jet links are created with the **route** attribute. Here's an example of a Jet link:

```javascript
export default {
    template:'<a route="/top/data">Data</a>'
}
```

After you click on the link, the app UI will be rebuilt and will consist of the main view _top_ and a subview _data_. Note that there is no hashbang in the path.

### Route for Webix Controls and Various HTML Elements

The **route** attribute can be added not only to **\** elements, but also to Webix controls and other HTML elements.

```javascript
export default {
    template:'<span route="/top/data">Data</span>'
}
```

This is how you can add **route** to a button:

```javascript
// views/top.js
import {JetView} from "webix-jet";

export default class TopView extends JetView {
    config(){
        return {
            cols:[
                {
                    view:"button",
                    value:"List",
                    route:"data",
                    click:function(){
                        this.$scope.show(this.config.route);
                    }
                },
                { $subview: true }
            ]
        };
    }
}
```

**this** in the button handler refers to the button, and **this.$scope** references the Jet view class [\[1\]](navigation.md#footnotes).

### Jet Links with Parameters

You can pass one or more parameters with a Jet link:

```javascript
// one
export default {
    template:'<span route="/top/data?id=2">Data</span>'
}
// or several
export default {
    template:'<span route="/top/data?id=2&name=some">Data</span>'
}
```

## 2. Navigation with HTML links

You can use **href** links for navigation. The **href** attribute can only be added to **\** elements:

```javascript
export default {
    template:'<a href="#!/top/data">Data</a>'
}
```

You can pass one or more parameters in the link, e.g.:

```javascript
// one
export default {
    template:'<a href="#!/top/data?id=2">Data</a>'
}
// or more
export default {
    template:'<a href="#!/top/data?id=2&name=some">Data</a>'
}
```

## 3. app.show\(\)

Apart from links, you can use the **show\(\)** method of app to switch views. **app.show\(\)** will rebuild the whole app or app module that called the method. A specific instance of the related view class is referenced with **this** if your handler is an _arrow function_ [\[1\]](navigation.md#footnotes). Use **this.app** to call the **show\(\)** method from control handlers, for instance:

```javascript
// views/toolbar.js
...
{ view:"button", value:"Details", click: () => {
    this.app.show("/demo/details");
}}
```

After a button click, the URL will change, and the app UI will be rebuilt according to it.

### _app.show\(\)_ with URL parameters

You can pass one or more parameters with the URL:

```javascript
// one
this.app.show("/demo/details?id=2");
// or many
this.app.show("/demo/details?id=2&name=some");
```

## 4. view.show\(\)

You can also change the URL by calling the **show\(\)** method from a specific view. A specific instance of the related view class is referenced with **this** from a handler that is defined as an _arrow function_ [\[1\]](navigation.md#footnotes). Calling **show\(\)** from a view gives you more freedom, as it allows rebuilding only this view or only its subview, not the whole app or app module. For example, suppose you have a view like this:

```javascript
// views/layout.js
...
cols:[
    { view:"button", value:"demo"},
    { $subview:true }
]
```

If the current URL is _"/layout/details"_, the subview is **details**. To replace **details** with a different subview, specify the name as it is or with _./_, and pass it to **this.show\(\)**:

```javascript
// views/layout.js
...
{ view:"button", value:"demo", click: () => {
    this.show("demo");
}}

//or

{ view:"button", value:"demo", click: () => {
    this.show("./demo");
}}
```

The resulting URL is going to be _/layout/demo_.

If you want to rebuild the whole app and load the **demo** view as the only view, specify the name of the view with a slash:

```javascript
// views/toolbar.js
...
{ view:"button", value:"demo", click: () => {
    this.show("/demo");
}}
...
```

### Navigating to Upper Levels

The syntax of showing views resembles the way you navigate through directories. So you can move some levels up from _"/layout/details"_, for example:

```javascript
{ view:"button", value:"bigview", click: () => {
    this.show("../../bigview");
}}
```

As a result, the app URL will be _/bigview_.

### _view.show\(\)_ with URL parameters

You can pass one or more parameters to **show\(\)** alongside the URL:

```javascript
// one
this.show("details?id=2");
// or many
this.show("details?id=2&name=some");
```

## Footnotes

#### \[1\]:

To read more about how to reference apps and view classes, go to ["Referencing views"](referencing-views.md).

