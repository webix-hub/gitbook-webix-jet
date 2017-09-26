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

Apps created with Webix Jet are single-page. To manipulate multiple views and app modules, you can nest views and thus create subviews.

### Direct Injection

One of the ways to nest a view is to inject a view class. Let's create a new view in _bigview.js_ and inject the **MyView** class in it:

```js
/* views/bigview.js */
import {MyView} from "myview" //?
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
index.html#!/BigView
```

### URL Navigation

You can enable embedding multiple views that will change according to the URL.

```js
export class BigView extends JetView {
    config() => {
        rows:[
            MyView,
            { $subview:true }
        ]
    }
}
```

The next segment of the URL that comes after **BigView** will be loaded as a subview. You can view the subviews by changing the URL, for example:

```
index.html#!/BigView/ViewA
index.html#!/BigView/ViewB
```



