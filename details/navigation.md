There are several ways to navigate through an app with Webix Jet.

## 1\. Jet Links

Jet links are created with the **route** attribute. Here's an example of a Jet link:

```js
export default {
    template:'<a route="/top/data">Data</a>'
}
```

After you click on the link, the app UI will be rebuilt and will consist of the main view *top* and a subview *data*. Note that there is no hashbang in the path.

### Route for Webix Controls and Various HTML Elements

The **route** attribute can be added not only to **\<a\>** elements, but also to Webix controls and other HTML elements. 

```js
export default {
    template:'<span route="/top/data">Data</span>'
}
```

This is how you can add **route** to a button:

```js
// views/top.js
import {JetView} from "webix-jet";

export default class TopView extends JetView {
    config(){
        return {
            cols:[
                { view:"button", value:"List", route:"data",
                  click:function(id){
                    var button = this;
                    this.$scope.show(button.route);
                }},
                { $subview: true }
            ]
        };
    }
}
```

**this** in the button handler refers to the button, and **this.$scope** references the Jet view class [[1]](#footnotes).

### Jet Links with Parameters

You can pass one or more parameters with a Jet link:

```js
// one
export default {
    template:'<span route="/top/data?id=2">Data</span>'
}
// or several
export default {
    template:'<span route="/top/data?id=2&name=some">Data</span>'
}
```

## 2\. Navigation with HTML links

You can use **href** links for navigation. The **href** attribute can only be added to **\<a\>** elements:

~~~js
export default {
    template:'<a href="#!/top/data">Data</a>'
}
~~~

You can pass one or more parameters in the link, e.g.:

```js
// one
export default {
    template:'<a href="#!/top/data?id=2">Data</a>'
}
// or more
export default {
    template:'<a href="#!/top/data?id=2&name=some">Data</a>'
}
```

## 3\. app.show\(\)

Apart from links, you can use the **show()** method of app to switch views. **app.show\(\)** will rebuild the whole app or app module that called the method. A specific instance of the related view class is referenced with **this** if your handler is an *arrow function* [[2]](#footnotes). Use **this.app** to call the **show()** method from control handlers, for instance:

```js
// views/toolbar.js
...
{ view:"button", value:"Details", click: () => {
    this.app.show("/demo/details");
}}
```

After a button click, the URL will change, and the app UI will be rebuilt according to it.

### _app.show()_ with URL parameters

You can pass one or more parameters with the URL:

```js
// one
this.app.show("/demo/details?id=2");
// or many
this.app.show("/demo/details?id=2&name=some");
```

## 4\. view.show\(\)

You can also change the URL by calling the **show\(\)** method from a specific view. A specific instance of the related view class is referenced with **this** from a handler that is defined as an *arrow function* [[3]](#footnotes). Calling **show()** from a view gives you more freedom, as it allows rebuilding only this view or only its subview, not the whole app or app module. For example, suppose you have a view like this:

```js
// views/layout.js
...
cols:[
    { view:"button", value:"demo"},
    { $subview:true }
]
```

If the current URL is *"/layout/details"*, the subview is **details**. To replace **details** with a different subview, specify the name as it is or with *./*, and pass it to **this.show()**:

```js
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

```js
// views/toolbar.js
...
{ view:"button", value:"demo", click: () => {
    this.show("/demo");
}}
...
```

### Navigating to Upper Levels

The syntax of showing views resembles the way you navigate through directories. So you can move some levels up from *"/layout/details"*, for example:

```js
{ view:"button", value:"bigview", click: () => {
    this.show("../../bigview");
}}
```

As a result, the app URL will be */bigview*.

### _view.show()_ with URL parameters

You can pass one or more parameters to **show()** alongside the URL:

```js
// one
this.show("details?id=2");
// or many
this.show("details?id=2&name=some");
```

<!-- footnotes -->

---
#### [Footnotes]:
To read more about how to reference apps and view classes, go to ["Referencing views"](../detailed/referencing.md).