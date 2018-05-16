# Creating Views

## View Concept

Views are modules for interface elements that should be kept in separate files. This makes the code loosely coupled. If some part of the app is messed up, the rest of it works. Parts of an app can be developed and tested independently. All visual components of the UI are separated from each other and can be reused. All the above said is critical for large and huge apps. Besides, the code looks neater.

All views should be stored in the **views** folder: one view per file. Views can be defined as ES6 classes that inherit from the _JetView_ class. For example, this is how a view module is defined in _views/myview.js_:

```javascript
// views/myview.js
import {JetView} from "webix-jet";

export default class MyView extends JetView{
    config: () => { template:"MyView text" };
}
```

Views are resolved one by one according to URL chunks in the address bar. _The local path to a file has nothing to do with the path in the address bar._ You can show the view by opening this path:

```text
index.html#!/myview
```

You can also include view files into subfolders:

views

* start.js
* details
  * form.js
  * template.js

To show the files in subfolders, reference them as _folder.file_, e.g.

```text
\#!details.form
\#!details.template
```

## Subview

Apps created with Webix Jet are single-page. The interface of your app can be constructed from multiple views. Views can be included in other views and are called _subviews_. They can be either static or dynamic.

### Static Subviews

One of the ways to nest a view is to import Jet view class and directly include it into another view. Let's create a new view in _bigview.js_ and include the **MyView** class into it:

```javascript
// views/bigview.js
import {JetView} from "webix-jet";
import MyView from "views/myview";

export default class BigView extends JetView{
    config: () => {
        rows:[
            MyView,
            { template:"BigView text" }
    ]}
}
```

You can open the view with this URL:

```text
index.html#!/bigview
```

### Dynamic Subviews

You can embed multiple views that will change according to the URL. Instead of the concrete name of the view class, write _{ $subview: true }_:

```javascript
// views/bigview.js
import {JetView} from "webix-jet";

export default class BigView extends JetView {
    config: () => { 
            rows:[
                { $subview: true },
                { template:"BigView text" }
    ]}
}
```

The next segment of the URL \(after _bigview_\) will instruct the app, which view to load as a subview. Based on the URL, the view file will be located, the related view will be loaded, and the corresponding view module will be rendered as a subview. For example:

```text
//load views/myview.js
index.html#!/bigview/myview

//load views/viewa.js
index.html#!/bigview/viewa

//load views/viewb.js
index.html#!/bigview/viewb
```

## Further reading

For more information on other ways to define views and include subviews, read:

* [Views and Subviews](../part-ii-webix-jet-in-details/views-and-subviews.md).

