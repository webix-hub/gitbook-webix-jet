## View Concept

Views are modules for interface elements that are usually kept in separate files. This prevents from meddling with the code. If some part of the app is messed up, the rest of it works. All visual components of the UI are separated from each other and can be reused. Parts of an app can be developed and tested independently. All the above said is critical for large and huge apps. Besides, thus code looks neater.

All views are stored in the **views** folder: one view per file. For example, this is how a view module is defined in _myview.js_:

```js
/* views/myview.js */
export class MyView extends JetView {
    config() => { template:"MyView text" };
}
```

You can show the view with this URL:

```
index.html#!/MyView
```

## Subview

Apps created with Webix Jet are single-page. Interface of your app can be constructed from multiple views. Some parts can be dynamic, so you may change them based on state of the app. Such dynamic views are called - subviews. 

#### Direct including

One of the ways to nest a view is to inject a view class. Let's create a new view in _bigview.js_ and inject the **MyView** class in it:

```js
/* views/bigview.js */
import {MyView} from "myview"

export class BigView extends JetView {
    config() => { 
            rows:[
                MyView,
                {
                    template:"BigView text"
                }
            ]   
        }
    }
}
```

You can open the view with this URL:

```
index.html#!/bigview
```

#### Dynamic including 

You can enable embedding multiple views that will change according to the URL.

```js
/* views/bigview.js */
export class BigView extends JetView {
    config() => { 
            rows:[
                { $subview: true },
                {
                    template:"BigView text"
                }
            ]   
        }
    }
}
```

The next segment of the URL that instruct the app, which view to load. Based on url name the view file will be locaed and related class will be loaded. You can view the subviews by changing the URL, for example:

```
//load sources/view/myview.js
index.html#!/bigview/myview

//load sources/view/viewa.js
index.html#!/bigview/viewa

//load sources/view/viewb.js
index.html#!/bigview/viewb
```