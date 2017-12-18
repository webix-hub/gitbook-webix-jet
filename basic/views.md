# <span id="contents">Creating Views</span>

- [View Concept](#view)
- [Static Subviews](#stat_subview)
- [Dynamic Subviews](#dynam_subview)

## [<span id="view">View Concept &uarr;</span>](#contents)

Views are modules for interface elements. Views should be kept in separate files. This makes the code loosely coupled. If some part of the app is messed up, the rest of it works. Parts of an app can be developed and tested independently. All visual components of the UI are separated from each other and can be reused. All the above said is critical for large and huge apps. Besides, the code looks neater.

All views should be stored in the **views** folder: one view per file. For example, this is how a view module is defined in *views/myview.js*:

```js
// views/myview.js
import {JetView} from "webix-jet";

export default class MyView extends JetView{
    config: () => { template:"MyView text" };
}
```

You can show the view by opening this path:

```
index.html#!/myview
```

## [<span id="stat_subview">Subview &uarr;</span>](#contents)

Apps created with Webix Jet are single-page. The interface of your app can be constructed from multiple views. Some parts can be dynamic, so you may change them based on the state of the app. Such dynamic views are called *subviews*. 

#### Direct (Static) Including

One of the ways to nest a view is to directly include a view class. Let's create a new view in *bigview.js* and include the **MyView** class into it:

```js
// views/bigview.js
import {JetView} from "webix-jet";
import MyView from "myview";

export default class BigView extends JetView{
    config: () => { 
        rows:[
            MyView,
            {
                template:"BigView text"
            }
    ]}
}
```

You can open the view with this URL:

```
index.html#!/bigview
```

#### [<span id="dynam_subview">Dynamic Including &uarr;</span>](#contents)

You can enable embedding multiple views that will change according to the URL. Instead of the concrete name of the view class, write *{ $subview: true }*:

```js
// views/bigview.js
export default class BigView extends JetView {
    config: () => { 
            rows:[
                { $subview: true },
                { template:"BigView text" }
    ]}
}
```

The next segment of the URL (after *bigview*) will instruct the app, which view to load as a subview. Based on the URL, the view file will be located, the related view will be loaded, and the corresponding view module will be rendered as a subview.

You can show subviews by changing the URL, for example:

```
//load views/myview.js
index.html#!/bigview/myview

//load views/viewa.js
index.html#!/bigview/viewa

//load views/viewb.js
index.html#!/bigview/viewb
```

## Further reading

For more information on other ways to define views and include subviews, read:

- [Views and Subviews](../details/subviews.md).