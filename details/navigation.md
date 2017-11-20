# Navigation

There are several ways to navigate through an app with Webix Jet:

- [Jet links](#jet)
- [HTML links](#html)
- [app.show](#app_show)
- [view.show](#view_show)

### <span id="jet">1. Jet Links</span>

Jet links are created with the **route** attribute instead of the usual **href**. The **route** attribute allows you to stop users from leaving the current view. This can be useful in the case of unsaved data, for instance. See the details in the [section on plugins](plugins.md). Here's an example of a Jet link:

```html
<a route="/details/data"></a>
```

After you click on the link, the app UI will be rebuilt and will consist of the main view *details* and a subview *data*. Note that there is no hashbang in the path.

The **route** attribute can be assigned not only to links, but to *any control* like a button or a menu option.

##### Jet Links with Parameters

You can pass one or more parameters with a Jet link:

```html
<!-- one -->
<a route="/details/data?id=2"></a>
<!-- or several -->
<a route="/details/data?id=2&name=some"></a>
```

### <span id="html">2. Navigation with HTML links</span>

Apart from navigating with links with the **route** attribute, you can create HTML links. This way is not so convenient, as the first one. You cannot prevent users from leaving the current view through an HTML link. In case there's no worries and you don't plan to guard users' unsaved data, you can create links with the **href** attribute. Suppose you have these view modules:

~~~js
// views/demo.js 
import {ToolbarView} from "toolbar";

const DemoView = {
    rows: [
        ToolbarView,
        { $subview: true }
    ]
};

// views/details.js
const DetailsView = {
    template: "App"
};
~~~

This is a link to the **Details** view as a subview of **DemoView**:

~~~html
<!--index.html-->
<a href="#!/demo/details">Data</a>
~~~

You can pass one or more parameters in the link, e.g.:

```html
<a href="#!/demo/details?id=2">Data</a>
<!-- or more -->
<a href="#!/demo/details?id=2&name=some">Data</a>
```

### <span id="app_show">3. app.show\(\)</span>

Apart from links, you can use the **show** method of app to switch views. **app.show\(\)** will rebuild the whole app or app module that called the method. A specific instance of the related view class is referenced with **this** if your handler is an *arrow function*<sup><a href="#myfootnote1" id="origin1">1</a></sup>. Reference app as **this.app** to call the **show** method from control handlers, for instance:

```js
// views/toolbar.js
...
{ view:"button", value:"details", click: () => {
    this.app.show("/demo/details");
}}
```

After a button click, the URL will change, and the app UI will be rebuilt according to it.

##### app.show with URL parameters

You can pass one or more parameters with the URL:

```js
// one
this.app.show("/demo/details?id=2");
// or many
this.app.show("/demo/details?id=2&name=some");
```

### <span id="view_show">4. view.show\(\)</span>

You can also change the URL by calling the **show\(\)** method from a specific view. A specific instance of the related view class is referenced with **this** from a handler that is defined as an *arrow function*<sup><a href="#myfootnote2" id="origin2">2</a></sup>. Calling **show** from a view gives you more freedom, as it allows rebuilding only this view or only its subview, not the whole app or app module. For example, suppose you have a view like this:

```js
// views/layout.js
...
cols:[
    { view:"button", value:"demo"},
    { $subview:true }
]
```

if the current URL is *"/layout/details"*, the subview is **details**. To replace **details** with a different subview, specify the name as it is or with *./*, and pass it to **this.show**:

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

The resulting URL is going to be */layout/demo*.

If you want to rebuild the whole app and load the **demo** view as the only view, specify the name of the view with a slash:

```js
// views/toolbar.js
...
{ view:"button", value:"demo", click: () => {
    this.show("/demo");
}}
...
```

##### Navigating to Upper Levels

The syntax of showing views resembles the way you navigate through directories. So you can move some levels up from *"/layout/details"*, for example:

```js
{ view:"button", value:"bigview", click: () => {
    this.show("../../bigview");
}}
```

As a result, the app URL will be */bigview*.

##### view.show with URL parameters

You can pass one or more parameters to show alongside the URL:

```js
// one
this.show("details?id=2");
// or many
this.show("details?id=2&name=some");
```

<!-- footnotes -->
- - -
<a id="myfootnote1" href="#origin1">1 &uarr;</a>, <a id="myfootnote2" href="#origin2">2 &uarr;</a>:
To read more about how to reference apps and view classes, go to ["Referencing views"](../detailed/referencing.md).