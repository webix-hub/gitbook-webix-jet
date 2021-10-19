# JetView Life-time Handler Methods

## config\(\)

This method returns the initial UI configuration of a view:

```javascript
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
    config(){
        return {
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", options:["details", "dash"] }
            ]
        };
    }
}
```

## init\(\)

The method is called once when a view is rendered. It is a good place to load some common data or to change the initial UI configuration of a view returned by [config\(\)](views.md#config).

**init\(\)** receives two **parameters**:

1. **view** (Webix view) - the view UI,
2. **url** - the app URL as an array.

Each array element of **url** is an object that contains three properties:

* **page** - the name of the URL element,
* **params** - parameters that you can pass with the URL,
* **index** - the index of the URL element \(beginning from 1\).

Let's use **init\(\)** to change the state of a control in a simple UI:

```javascript
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
    config(){
        return {
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", options:["details", "dash"] }
            ]
        };
    }
    init(view, url){
        if (url.length > 1) //if there is a subview
            view.queryView({
                view:"segmented"
            }).setValue(url[1].page);
    }
}
```

The segmented button is referenced by the Webix method [**queryView\(\)**](https://docs.webix.com/api__ui.baseview_queryview.html), while **view** is the top Webix widget in the view. _url\[1\].page_ is the name of the current subview \(_details_ or _dash_\).

This is how you can load data to a Jet class view:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {records} from "models/records";

export default class DataView extends JetView{
    config(){
        return {
            view:"datatable", autoConfig:true
        };
    }
    init(view){
        view.parse(records);
    }
}
```

## urlChange\(\)

**urlChange\(\)** is called every time the app URL is changed. It reacts to the change in the URL after **!\#** [\[1\]](views.md#1).


### When urlChange is called

1. if a URL segment is changed, **urlChange\(\)** is called for the view that is rendered from that segment and for its parent

Consider the following example. The initial URL is:

```text
/layout/demo/details
```

If you change it to:

```text
/layout/demo/preview
```

**urlChange\(\)** will be called for **preview** and **demo**. **demo** is not reconstructed. Such approach allows preserving parts of UI not affected by navigation, which improves performance and UX of the app. It is especially important if you have some complex widget in the parent view and do not want to fully reconstruct/reload it on subview navigation.

2. if a URL parameter is changed, **urlChange\(\)** is called for the view-owner of the parameter and for its kids.

For example, if you have the view structure like the following:

```text
/layout/demo/details
```

and set the **id** param for **layout** via [setParam](../api/jetview-methods.md#this-setparam) or [show](../api/jetview-methods.md#this-show), **urlChange\(\)** of **layout**, **demo**, and **details** will be called.

### Method parameters

The **urlChange** method receives two **parameters**:

* **view** - the Webix widget inside the Jet view class
* **url** - the URL as an array of URL elements

### Example

**urlChange\(\)** can be used to restore the state of the view according to the URL, e.g.:

```javascript
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
    config(){
        return {
            view:"toolbar",
            elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"segmented", options:["details", "dash"] }
            ]
        };
    }
    urlChange(_view, url){
        if (url.length > 1){
            this.$$("segmented").setValue(url[1].page);
        }
    }
}
```

## ready\(\)

**ready\(\)** is called when the current view and all its subviews have been rendered. For instance, if the URL is changed to _a/b_, the order in which view class methods are called is the following:

```text
config a
init a
    config b
    init b
    urlChange b
    ready b
urlChange a
ready a
```

**ready\(\)** receives two **parameters**:

* **view** - the Webix widget inside the Jet view class
* **url** - the URL as an array of URL elements

Here's how you can use **ready\(\)**. There are two simple views, a list and a form for editing the list:

```javascript
// views/list.js
const list = {
    view: "list",
    select: true,
    template: "#value#",
    data: [{value:"one"},{value:"two"}]
};

// views/form.js
const form = {
    view: "form",
    elements:[
        {view: "text", name: "value", label: "Value"},
        {view: "button", value: "Save", width: 90}
    ]
};
```

Let's include these views into one module and bind the list to the form:

```javascript
// views/listedit.js
import {JetView} from "webix-jet";

export default class ListEditView extends JetView{
    config(){
        return {
            cols:[
                { $subview:"list" },   //load "views/list"
                { $subview:"form" }    //load "views/form"
            ]
        }
    }
    ready(view){
        const form = view.queryView({view:"form"});
        const list = view.queryView({view:"list"});
        form.bind(list);
    }
}
```

In the example, the form will be bound to the list only when both the list and the form are rendered.

## destroy\(\)

**destroy\(\)** is called only once for each class instance when the view is destroyed \(closed and no longer present in the app URL\).

E.g. you can use **destroy\(\)** to detach event listeners that will not be destroyed automatically or clean some temporary data.

```javascript
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    init(){
        this.ev = webix.event(window, "resize", e => {
            /* do something */
        });
    }
    destroy(){
        webix.eventRemove(this.ev);
    }
}
```
