# Creating Complex Widgets

To create simple and medium-sized widgets that enclose some third party library components or a few Webix widgets, you can use [webix.protoUI\(\)](https://docs.webix.com/desktop__custom_component.html). Custom widgets must be based on **webix.ui.view**, while custom layouts must be based on **webix.ui.layout** and have the layout configuration defined in the **$init** method.

For complex cases, when you want to create a widget with multiple layouts, dynamic views, complex data, etc., it is better to use Webix Jet.

## How to Create

Create a Jet app and convert it into a new widget with **webix.protoUI\(\)** with **webix.ui.jetapp** as the base:

```javascript
// sources/newwidget.js
import {JetApp} from "webix-jet";
export default class NewWidgetApp extends JetApp {
    constructor(){
        const defaults = {
            id		: APPNAME,
            version	: VERSION,
			debug	: !PRODUCTION,
			router	: EmptyRouter,
            start	: "/top/start"
        };
        super({ ...defaults, ...config });
    }
}

// this!
if (!BUILD_AS_MODULE){
    webix.ready(() => new MyApp().render() );

webix.protoUI({
    name:"new-widget",
    app: NewWidgetApp
}, webix.ui.jetapp);
```

Later this widget can be used like an ordinary Webix widget \(included on a page, in Webix layout, resized, etc.\):

```javascript
webix.ui({
    rows:[
        { view:"toolbar", elements:[...] },
        {
            cols:[ { menu }, { view:"new-widget" } ]
        }
    ]
});
```

## Very Important

If you plan to use Jet app as a widget, keep in mind a few things:

_1. **Do not use** any hard-coded **"id"** values_.

This will prevent initializing more than one instance of the widget, because the IDs will be no longer unique. Instead, use **localId** and locate widgets inside the view with [this.$$\(\)](../part-ii-webix-jet-in-details/jetview-api.md#this-usdusd) or **queryView\(\)**.

_2. Use **services** instead of **models** for data loading_.

Models are shared between all instances of a widget, so a change in the data of one instance of the widget will reflect in all other instances. A common use-case will be to have all instances with separate states and unique data models.

Instead of importing a model as usual:

```javascript
import {data} from "models/records";
```

modify the model in such a way, that a new DataCollection should be created for every instance:

```javascript
// models/records.js
function init(){
    const data = new webix.DataCollection({
        url:"data/records.php"
    });
    return { data };
}
```

Then import the model and set a service that will create a new DataCollection when necessary:

```javascript
// app.js
import {JetApp} from "webix-jet";
import {records} from "models/records";

export default class MyApp extends JetApp {
    constructor(config){
        super(config);
        this.setService("records", records()); 
    }
}
...
```

Later any view inside the widget can get a new copy of data model with the help of the service:

```javascript
// views/myview.js
import {JetView} from "webix-jet";
export default class MyView extends JetView {
    ...
    init(){
        this.data = this.app.getService("records");
    }
}
```

## Dynamic Widget Loading

If you want to load custom widget code on demand, you can split your code and import the bundles with widget code when it is needed. For example, widgets can be imported in [config\(\)](../part-ii-webix-jet-in-details/views-and-subviews.md#config) of a Jet view:

```javascript
// views/statistics.js
import {JetView} from "webix-jet";
export default class StatisticsView extends JetView{
    config(){
        var widgets = import(/* webpackChunkName: "widgets" */ "modules/customgrid");
        return widgets.then(() => {

            return { rows:[
                { type:"header", template:"Sales 2018" },
                { view:"custom-grid" }
            ]};

        });
    }
}
```

{% hint style="info" %}
This works for both custom Webix widgets and Jet apps as custom widgets.
{% endhint %}

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/bundles.js)

