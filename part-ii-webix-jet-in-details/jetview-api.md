# JetView API

The JetView class has the following methods:

| Method | Use it to |
| :--- | :--- |
| getParam\(name, parent\) | give the API access to the URL parameters |
| getParentView\(\) | call methods of a parent view |
| getRoot\(\) | call methods of the Webix widget |
| getSubView\(name\) | call the methods of a subview |
| getSubViewInfo\(name\) | get the object with the info on the subview and a pointer to its parent |
| getUrl\(\) | get the URL segment related to a view |
| on\(app,"event:name",handler\) | attach an event |
| refresh\(\) | repaint the view and its subviews |
| setParam\(name, value\) | set the URL parameters |
| show\("path"\) | show a view or a subview |
| ui\(view\) | create a popup or a window |
| use\(plugin, config\) | switch on a plugin |
| $$\("controlID"\) | return Webix widgets inside a view |

## this.getParam\(\)

Use **this.getParam\(\)** method to get the URL related data. **getParam\(\)** lets the API access the URL parameters \(variables\), including those of the parent view. This can be useful as views and subviews quite often share a common parameter.

**getParam\(\)** takes two parameters:

* _name_ - \(mandatory, string\) the name of the parameter;
* _parent_ - \(optional, Boolean\) _false_ by default; if _false_, it looks among those parameters that belong to the view that called the method; if _true_, it looks for parameters of the parent views.

For example, the URL is:

```text
#!/top/page?id=12/some
```

**id** is the parameter of the parent view **page**.

This is how you can get **id** from the **page** view:

```javascript
//from page.js
var id = this.getParam("id"); //id == 12
```

And this is how you can get **id** from **some**:

```javascript
//from some.js
var id = this.getParam("id");       //id == ""
var id = this.getParam("id", true); //id == 12
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

## this.getParentView\(\)

Use **this.getParentview\(\)** to get to the methods of the parent view. In this example, the child refers to its parent view with **this.getParentView\(\)** and calls its **getSelected\(\)** method:

```javascript
// views/form.js
import {JetView} from "webix-jet";

export default class Child extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" }
            ]
        };
    }
    init(view){
        var item = this.getParentView().getSelected();
        view.setValues(item);
    }
}
```

For more details, [read the "Referencing" section](referencing-views.md).

## this.getRoot\(\)

Use **this.getRoot\(\)** to call methods of a Webix widget inside a Jet class view.

```javascript
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    config(){
        return { 
            view:"form", elements:[
                { view:"text", name:"email", required:true, label:"Email" },
                { view:"button", value:"save", 
                    click: () => {
                        if (this.getRoot().validate())
                            this.show("details");
                }}
        ]};
    }
}
```

For more details on referencing views, [read the "Referencing" section](referencing-views.md).

## this.getSubView\(\)

Use **this.getSubView\(\)** if you want to get to the methods of a subview. It looks for a subview by its name, which you will have to give to the subview. You can do it like this:

```javascript
// views/listedit.js
import {JetView} from "webix-jet";

export default class ListEditView extends JetView{
    config(){
        return {
            cols:[
                { $subview:"list", name:"list" },       //load "views/list"
                { $subview:"form", name:"form" }        //load "views/form"
            ]
        }
    }
}
```

After you set the name to a subview, you can refer to it with **this.getSubView\(name\)** from the methods of the parent:

```javascript
// views/listedit.js
import {JetView} from "webix-jet";

export default class ListEditView extends JetView{
    ...
    ready(){
        const list = this.getSubView("list").getRoot();
        const form = this.getSubView("form").getRoot();
        form.bind(list);
    }
}
```

If you call **getSubView\(\)** without a parameter, it will return the view that is currently open as the subview of the calling view. In the example below, **OtherView** will be returned.

{% code-tabs %}
{% code-tabs-item title="views/top.js" %}
```javascript
export default class TopView extends JetView { 
  config(){
    return {
      rows:[
        {
          view:"button", value:"getSubView",
          click:() => console.log(this.getSubView())
        }, 
      	{
          cols:[
          	{ $subview:SomeView },
            { $subview:true }  // the view that is shown here will be returned
          ]
        }
      ]
    };
  }
  init(){
     this.show("other");
     // show 'views/other.js' which e.g. contains the OtherView class
  }
}

```
{% endcode-tabs-item %}
{% endcode-tabs %}

For more details on referencing views, [read the "Referencing" section](referencing-views.md).

## this.getSubViewInfo\(\)

This is the extended version of the [getSubView\(\)](https://webix.gitbook.io/webix-jet/~/edit/drafts/-LMb4Cn6Mp-grEk06Tyj/part-ii-webix-jet-in-details/jetview-api#this-getsubview) method. It returns the object with the info on the subview. The object contains 2 properties:

1. **subview** which has

   1. **id** - the ID of the top Webix widget inside the subview, 
   2. **view** - the subview itself \(this is the return value of [getSubView\(\)](https://webix.gitbook.io/webix-jet/~/edit/drafts/-LMb4Cn6Mp-grEk06Tyj/part-ii-webix-jet-in-details/jetview-api#this-getsubview)\);

2. **parent** which is the parent view of the subview.

## this.on\(\)

Use **this.on\(\)** to attach events. This way of attaching an event is convenient, because it automatically detaches the event when the view that called it is destroyed. This helps to avoid memory leaks that may happen, especially in older browsers.

```javascript
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView{
    init(){
        this.on(this.app, "save:form", function(){
            this.show("aftersave");
        });
    }
}
```

For more details on attaching and calling events, read the ["View Communication" section](view-communication.md).

## this.refresh\(\)

Use the **refresh\(\)** method to repaint the view and its subviews after some changes in the UI:

```javascript
// views/top.js
this.refresh();
```

When the method is called, the following happens:

* all subviews are destroyed,
* the **config\(\)** of the view is called and the UI is created,
* **init** handlers of the view and its subviews are triggered,
* subviews are recreated.

{% hint style="warning" %}
**Important:** the **destroy\(\)** handler for the refreshed view is not called. That is why you need to provide safeguards in **init\(\)** to prevent double initialization.
{% endhint %}

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/refresh.js)

## this.setParam\(\)

Use **this.setParam\(\)** method to set the URL related data. You can use **setParam\(\)** to change a URL segment or a URL parameter.

**setParam\(\)** has three parameters:

* the _name_ of the URL parameter,
* the new _value_,
* \(optional\) _display_, if it is set to _true_, the parameter is displayed in the URL.

For example, this is how you can change a URL parameter:

```javascript
this.setParam("mode", "12", true); // some?mode=12
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

## this.show\(\)

This method is used for in-app navigation. It loads view modules according to the path specified as a parameter:

```javascript
// views/toolbar.js
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "button", value:"Details",
            click: () => {
                this.show("details");
            }
    }]
};
export default Toolbar;
```

**show\(\)** returns a _promise_. This is useful, when you plan to do something after a subview is rendered.

```javascript
// views/toolbar.js
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "button", value:"Details",
            click: () => {
                this.show("details").then(/* do something */);
            }
    }]
};
export default Toolbar;
```

### Optional _target_ Parameter

If a view has several _$subview:true_ placeholders, you might want to show a subview instead of a certain placeholder. **this.show\(\)** has an optional parameter that will make it possible. You must give the subview placeholder a name and pass this name to **this.show\(\)**:

```javascript
// views/big.js
...
{
    cols:[
        { $subview:true },
        { $subview:true, name:"right" },
    ]
}
...
this.show("small", { target:"right" })
```

_small_ is the name of a subview that will be placed in the right column of the _big_ view.

The above code works the same as this:

```javascript
this.getSubView("right").show("small");
```

_getSubView_ returns a subview by the name passed as a parameter.

For more details on view navigation, [read the "Navigation" article](../part-i-basic-usage/in-app-navigation.md).

## this.getUrl\(\)

Use **this.getUrl\(\)** method to get the URL as an array of segments, each one is an object with three properties:

* _page_ \(the name of the segment as _string_\)
* _params_ \(an object with URL parameters\)
* _index_ \(the number of the segment starting from 1\)

For example, you can get the URL segment related to the view by accessing the _page_ property:

```javascript
// views/some.js
import {JetView} from "webix-jet";

export default class SomeView extends JetView{
    config(){
        return {
            view:"button", value:"Show URL", click: () => {
                var url = this.getUrl();  //URL as an array of segments
                console.log(url[0].page); //"some"
            }
        };
    }
}
```

## this.ui\(\)

**this.ui\(\)** call is equivalent to **webix.ui\(\)**. It creates a new instance of the view passed to it as a parameter. For example, you can create views inside popups or modal windows with **this.ui\(\)**. The good thing about this way is that it correctly destroys the window or popup when its parent view is destroyed.

### _this.ui\(\)_ for Windows and Popups

This is a simple example of a Jet class with a window. The class has a method that can be used any other class view to show the window.

```javascript
//views/windows/window.js
import {JetView} from "webix-jet";
export default class WindowView extends JetView {
    config(){
        return {
            view:"window", body:{
                template:"Jet popup"
            }
        };
    }
    showWindow(){
      this.getRoot().show();
    }
}
```

Have a look at the parent view that instantiates the window with its **ui\(\)** method:

```javascript
//views/top.js
import {JetView} from "webix-jet";
import WindowView from "views/windows/window";
export default class TopView extends JetView {
    config(){
        return {
            view:"button", width:200, value:"Popup", 
            click:() => this._jetPopup.showWindow()
        };
    }
    init(){
        this._jetPopup = this.ui(WindowView);
    }
}
```

Note again that there is no need to destroy the window manually.

For more details about popups and windows, [go to the "Popups and Windows" section](popups-and-windows.md).

In the example above, a Jet view was passed to **this.ui\(\)**, so the return value of **this.ui\(\)** was also a Jet view. You can also pass Webix widgets or Jet views wrapped inside a Webix layout to the method.

### _this.ui\(\)_ with a Webix widget config

_this.ui\(\)_ can return a Webix UI object:

```javascript
// views/top.js
import {JetView} from "webix-jet";

export default class TopView extends JetView {
  ...
  init(){
      this.win = this.ui({ template:"test" });
  }
}
```

### _this.ui\(\)_ with Jet views inside a Webix layout

_this.ui\(\)_ can also return a Webix UI object with Jet views inside:

```javascript
// views/sub.js
import {JetView} from "webix-jet";

export default class SubView extends JetView {
    config(){
        return {template:"test"};
    }
};

// views/top.js
import {JetView} from "webix-jet";
import SubView from "views/sub";

export default class TopView extends JetView {
    ...
    init(){
        this.win = this.ui({
            rows:[
                SubView
            ]
        });
    }
}
```

### Optional _container_ Parameter

**this.ui\(\)** has an optional parameter - _container_. If you provide it, the view will be rendered inside the container:

```javascript
var SubView = {
      template:"test"
};

class TopView extends JetView {
    config(){
        return {};
    }
    init(){
        this.win = this.ui(SubView,{container:"here"});
    }
}
```

where _"here"_ is the ID of a _div_ element on the page. _container_ can be an ID or an HTML element, e.g.:

```javascript
this.win = this.ui(SubView,{container:document.getElementById("here")});
```

## this.use\(\)

The method launches a plugin. For example, this is how the **Status** plugin can be added:

```javascript
// views/data.js
...
init(){
    this.use(plugins.Status, { 
        target: "app:status",
        ajax:true,
        expire: 5000
    });
}
```

For more details on plugins, check out the ["Plugins" section](plugins.md).

## this.$$\(\)

Use **this.$$\(\)** to look for Webix widgets by their local IDs inside the current Jet view [\[1\]](jetview-api.md#1).

```javascript
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView {
    config() {
        //...
        { view: "segmented", localId: "control", options: ["details", "dash"],
            click: function() {
                this.$scope.app.show("/demo/" + this.getValue());
        }}
    }
    init(ui, url) {
        if (url.length > 1)
            this.$$("control").setValue(url[1].page);
    }
}
```

For details, [read the "Referencing" section](referencing-views.md).

## Footnotes

#### \[1\]:

Starting with Webix Jet 1.5. If you want to look for widgets in the current view and all its subviews, use [this.getRoot\(\).queryView\(\)](https://docs.webix.com/api__ui.baseview_queryview.html).

