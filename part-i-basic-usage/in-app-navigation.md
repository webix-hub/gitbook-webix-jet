# In-app Navigation

_Routing_ means navigating from one part of the application to another. Navigation is implemented by changing the URL of the page. The URL reflects the current state of the app. By default, it is stored as a part after the hashbang. Only the part after the hashbang \(\#!\) is changed [\[1\]](in-app-navigation.md#1). When the URL is changed, the app updates itself accordingly.

## Advantages of Jet Navigation

* _Browser Navigation Keys_: Jet URL is stored in the browser history, so you can move backwards and forwards to previously opened subviews.
* _Refresh Friendly_: If you reload the page, the URL will stay the same and the state of the UI will be exactly the same as before a page reload.
* _Convenient Development_: If you work on some particular subview \(e.g. _games_\), you can open the path to it \(_\#!/games_\) and test it separately from the rest of the UI.

In the previous section, ["Creating views"](creating-views.md), you have read about direct URL navigation. There are three [\[2\]](in-app-navigation.md#2) more ways to show views and subviews:

## 1. Jet Links

You can add links with the **route** attribute and provide the URL to the desired views, e.g.:

```javascript
export default {
    template:'<a route="/top/data">Data</a>'
}
```

After you click on the link, the app UI will be rebuilt and will consist of the parent view _top_ and a subview _data_.

You can pass one or more **parameters** with a Jet link:

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

## 2. app.show\(\)

The **app.show\(\)** method is applied to the whole application and rebuilds its UI \(only the parts that changed\). You can call the method from control handlers, for instance.

Here is how you can rebuild the app UI with **app.show\(\)**. **this** refers to a specific instance of the related view class if you define handlers as _arrow functions_ [\[3\]](in-app-navigation.md#3-4).

```javascript
// views/layout.js
...
{ view:"button", value:"Details", click: () => {
    this.app.show("/demo/details");
}}
...
```

After a button click, the URL will change, and the app UI will be rebuilt according to it.

### _app.show\(\)_ with URL Parameters

You can pass one or more parameters to show alongside the URL:

```javascript
// views/layout.js
// one
this.app.show("/demo/details?id=2");
// or many
this.app.show("/demo/details?id=2&name=some");
```

## 3. view.show\(\)

### Rebuilding Part of the App

You can also change the URL by calling the **show\(\)** method of a specific view class. Showing subviews with **view.show\(\)** gives you more freedom, as it allows rebuilding only this view or only its subview, not the whole app or app module. For example, suppose you have a view like this:

```javascript
// views/layout.js
import {JetView} from "webix-jet";

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

If the current URL is _"/layout/details"_, the subview is **details**. Let's replace **details** with the **demo** subview on a button click. Pass the name of the subview as it is or with _"./"_ to **show\(\)**.

**this** refers to a specific instance of the related view class if you define a handler as an _arrow function_ [\[4\]](in-app-navigation.md#3-4). To rebuild a part of the UI, call **this.show\(\)**:

```javascript
// views/layout.js
import {JetView} from "webix-jet";

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
    { view:"button", value:"Demo", click: () => {
        this.show("./demo");
    }}
...
```

If you click **Demo**, the resulting URL is going to be _/layout/demo_.

### Rebuilding the whole app

If you want to rebuild the whole app and load the **demo** view as the only view, specify the name of the view with a slash:

```javascript
// views/layout.js
...
{ view:"button", value:"demo", click: () => {
    this.show("/demo");
}}
...
```

### _view.show\(\)_ with URL Parameters

You can pass one or more parameters with the URL:

```javascript
// views/layout.js
// one
this.show("demo?id=2");
// or many
this.show("demo?id=2&name=some");
```

## Further reading

This is all about Webix Jet in a nutshell.

You can also read these sections of Part II:

* [Navigation](../part-ii-webix-jet-in-details/navigation.md)
* [JetApp API](../part-ii-webix-jet-in-details/jetapp-api.md)
* [JetView API](../part-ii-webix-jet-in-details/jetview-api.md)

## Footnotes

#### \[1\]:

This is relevant for _HashRouter_, which is the default router. Hashbang is not displayed if you use _UrlRouter_. The app part of the URL isn't displayed at all if you use other types of routers. However, the app URL is stored for all the three routers except _EmptyRouter_ and the behavior is the same as if the URL were displayed. For more details, [see section "Routers"](../part-ii-webix-jet-in-details/routers.md).

#### \[2\]:

There is one more way, described in the advanced part, the ["Navigation"](../part-ii-webix-jet-in-details/navigation.md) chapter.

#### \[3\],\[4\]:

To read more about how to reference apps and view classes, go to ["Referencing views"](../part-ii-webix-jet-in-details/referencing-views.md).

