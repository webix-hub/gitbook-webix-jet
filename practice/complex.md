# Creating Complex Webix Widgets with Webix Jet

To create simple and medium-sized widgets that enclose some third party library components or a few Webix widgets, you can use **webix.protoUI()**. Custom widgets must be based on **webix.ui.view**, while custom layouts must be based on **webix.ui.layout** and have the layout configuration defined in the **$init** method.

For complex cases, when you want to create a widget with multiple layouts, dynamic views, complex data, etc., it is better to use Webix Jet. Create a Jet app and convert it into a new widget with **webix.protoUI()** with **webix.ui.jetapp** as the base:

```js
// sources/newwidgetapp.js
import {JetApp, routers} from "webix-jet";
export default class NewWidgetApp extends JetApp {
    //app config
    constructor(){
        super({
            ...
            router:EmptyRouter
        });
    }
}

webix.protoUI({
    name:"new-widget",
    app: NewWidgetApp
}, webix.ui.jetapp);
```

Later this widget can be used like an ordinary Webix widget (included on a page, in Webix layout, resized, etc.):

```js
webix.ui({
    rows:[
        { view:"toolbar", elements:[...] },
        {
            cols:[ { menu }, { view:"new-widget" } ]
        }
    ]
});
```

## Important

If you plan to use Jet app as a widget, keep in mind a few things:

_1\. **Do not use** any hardcoded **"id"** values_.

This will prevent initializing more than one instance of the widget, because the IDs will be no longer unique. Instead, use **localId** and locate widgets inside the view with **this.$$()** or **queryView()**. 

_2\. Use **services** instead of **models** for data loading_.

Models are shared between all instances of a widget, so a change in the data of one instance of the widget will reflect in all other instances. A common use-case will be to have all instances with separate states and unique data models.

Instead of importing a model as usual:

```js
import {data} from "models/records";
```

modify the model so that a new DataCollection should be created for every instance:

```js
// models/records.js
function init(){
    const data = new webix.DataCollection({
        url:"data/records.php"
    });
    return { data };
}
```

Then import the model and set a service that will create a new DataCollection when necessary:

```js
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

```js
// views/myview.js
import {JetView} from "webix-jet";
export default class MyView extends JetView {
    ...
    init(){
        this.data = this.app.getService("records");
    }
}
```