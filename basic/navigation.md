## Navigation (Routing)

Navigation is implemented by changing the URL of the page. The URL reflects the current state of the app. By default, it is stored as a part after the hashbang. Only the part after the hashbang \(\#!\) is changed<sup><a href="#myfootnote1" id="origin">1</a></sup>. When the URL is changed, the app updates itself accordingly. 

### Advantages of Jet Navigation

- *Browser Navigation Keys*: You can move backwards and forwards to previously opened subviews.
- *Refresh Friendly*: If you reload the page, the URL will stay the same and the state of the UI will be exactly the same as before page reload.
- *Convenient Development*: If you work on some particular subview (*games*), you can open the path to it (*#!/games*) and test it separately from the rest of UI.

In the previous section ["Creating views"](views.md), you have read about direct URL navigation. There are three more ways to show views and subviews.

### 1. Jet Links

You can add links with the **route** attribute instead of the usual **href** and provide the URL to the desired views, e.g.:

```html
<a route="/details/data"></a>
```

After you click on the link, the app UI will be rebuilt and will consist of the main view _details_ and a subview _data_.

You can pass one or more parameters with a Jet link:

```html
<a route="/details/data?id=2"></a>
<!-- or several -->
<a route="/details/data?id=2&name=some"></a>
```

### 2. app.show\(\)

The **app.show\(\)** method is applied to the whole application. You can call the method from control handlers, for instance.

A specific instance of the related view class is referenced with **this** if you define handlers as arrow functions. To reference the app, use **this.app**. To read more about how to reference apps and view classes, go to ["Referencing views"](../detailed/referencing.md).

Here is how you can rebuild the UI with **app.show**:

```js
// views/layout.js
...
{ view:"button", value:"Details", click: () => {
    this.app.show("/demo/details");
}}
...
```

After a button click, the URL will change, and the app UI will be rebuilt according to it.

You can pass one or more parameters to show alongside the URL:

```js
// views/layout.js

this.app.show("/demo/details?id=2");
// or many
this.app.show("/demo/details?id=2&name=some");
```

### 3. view.show\(\)

You can also change the URL by calling the **show\(\)** method of a specific view class. Showing subviews with **view.show** gives you more freedom, as it allows rebuilding only this view or only its subview, not the whole app or app module. For example, suppose you have a view like this:

```js
// views/layout.js

export default class LayoutView extends JetView {
    config(){
        return {
            rows:[
                { view:"button", value:"demo" },
                { $subview:true }
            ]
        };
    } 
}
```

If the current URL is _"/layout/details"_, the subview is **details**. Let's replace **details** with the **demo** subview on a button click. To replace the current subview with a different one, pass the name as it is or with *"./"* to the **show** method of the class view.

A specific instance of the related view class is referenced with **this** if you define handlers as arrow functions. To read about how you can reference view classes, go to ["Referencing views"](../detailed/referencing.md).

```js
// views/layout.js

export default class LayoutView extends JetView {
    config(){
        return {
            rows:[
                { view:"button", value:"demo", click: () => {
                    this.show("demo");
                }},
                { $subview:true }
            ]
        };
    } 
}

//or
...
    { view:"button", value:"demo", click: () => {
        this.show("./demo");
    }}
...
```

If you click **demo**, the resulting URL is going to be */layout/demo*.

If you want to rebuild the whole app and load the **demo** view as the only view, specify the name of the view with a slash:

```js
// views/layout.js
...
    { view:"button", value:"demo", click: () => {
        this.show("/demo");
    }}
...
```

You can pass one or more parameters to show alongside the URL:

```js
// views/layout.js

this.show("details?id=2");
// or many
this.show("details?id=2&name=some");
```

You can also read ["Navigation"](../details/navigation.md) in the advanced chapter.

This is all about Webix Jet in a nutshell. For more details, go on to the next chapter ["JetApp API"](../details./app.md).

<!-- footnotes -->
- - -
<a id="myfootnote1" href="#origin">1</a>:
This is relevant for *HashRouter*, which is the default router. Hashbang is not displayed if you use *UrlRouter*. The app part of the URL isn't displayed at all if you use other types of routers. However, the app URL is stored for all the three routers except *EmptyRouter* and the behavior is the same as if the URL were displayed. For more details, [see section "Routers"](../details/routers.md).