# JetView API

The JetView class has the following methods:

| Method | Use |
| :--- | :--- |
| contains() | returns *true* if the calling view contains SomeView |
| getParam\(\) | returns the URL parameters |
| getParentView\(\) | returns the parent view |
| getRoot\(\) | returns the top Webix widget in the view |
| getSubView\(\) | returns a subview of the view |
| getSubViewInfo\(\) | returns the object with the info on the subview and a pointer to its parent |
| getUrl\(\) | returns an array of the URL segments related to a view |
| getUrlString() | returns the URL as a string	|
| on\(\) | attaches an event |
| refresh\(\) | repaints the view and its subviews |
| setParam\(\) | sets the URL parameters |
| show\(\) | shows a view or a subview |
| ui\(\) | creates a popup or a window |
| use\(\) | enables a plugin |
| $$\(\) | returns Webix widgets inside a view |

## this.contains()

The method checks if the calling Jet view contains the view from the parameter.

**Parameters:** a Jet View.
**Returns:** boolean.

## this.getParam\(\)

Use **this.getParam\(\)** method to get the URL parameters. **getParam\(\)** lets the API access the URL parameters \(variables\), including those of the parent view. This can be useful as views and subviews quite often share a common parameter.

**Parameters:**
* _name_ - \(mandatory, string\) the name of the parameter;
* _parent_ - \(optional, boolean\) _false_ by default; if _false_, it looks among those parameters that belong to the view that called the method; if _true_, it looks for parameters of the parent views.

**Returns:** the parameter.

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

Use **this.getParentview\(\)** to get to the methods of the parent view.

**Parameters:** none.

**Returns:** a Jet view object.

In this example, the child refers to its parent view with **this.getParentView\(\)** and calls its **getSelected\(\)** method:

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

**Parameters:** none.

**Returns:** the top Webix view inside the calling Jet view. 

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

Use **this.getSubView\(\)** if you want to get to the methods of a subview. It looks for a subview by its name, which you will have to give to the subview.

**Parameters:**
- *name* (string, optional) is the name of the subview

**Returns:** a Jet view object.

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

If the calling view has several dynamic subviews, you can get to them by calling **getSubView()** with the name of the subview:

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
    ready(){
        const list = this.getSubView("list").getRoot();
        const form = this.getSubView("form").getRoot();
        form.bind(list);
    }
}
```

For more details on referencing views, [read the "Referencing" section](referencing-views.md).

## this.getSubViewInfo\(\)

This is the extended version of the [getSubView\(\)](https://webix.gitbook.io/webix-jet/~/edit/drafts/-LMb4Cn6Mp-grEk06Tyj/part-ii-webix-jet-in-details/jetview-api#this-getsubview) method. It returns the object with the info on the subview. The object contains 2 properties:

1. **subview** which has
   1. **id** - the ID of the top Webix widget inside the subview, 
   2. **view** - the subview itself \(this is the return value of [getSubView\(\)](https://webix.gitbook.io/webix-jet/~/edit/drafts/-LMb4Cn6Mp-grEk06Tyj/part-ii-webix-jet-in-details/jetview-api#this-getsubview)\);
2. **parent** which is the parent view of the subview.

## this.on\(\)

Use **this.on\(\)** to attach event handlers.

**Parameters:**
- *obj* (object) is the app that includes the calling view or *webix*/Webix view or module that calls the event,
- *name* (string) is the name of the event,
- *code* (function) is the event handler.

**Returns:**
- *void* (if *obj* is the app) or the *id* of the event handler.

This way of attaching an event handler is convenient, because it automatically detaches the event handler when the view that called it is destroyed. This helps to avoid memory leaks that may happen, especially in older browsers.

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

Use the **refresh\(\)** method to repaint the view and its subviews after some changes in the UI.

**Parameters:** none.

**Returns:** a promise.

```javascript
// views/top.js
this.refresh();

this.refresh().then(() => { /* ...do something */ });
```

When the method is called, the following happens:

* all subviews are destroyed,
* the **config\(\)** of the view is called and the UI is created,
* **init** handlers of the view and its subviews are triggered,
* subviews are recreated.

<!-- {% hint style="warning" %}
**Important:** the **destroy\(\)** handler for the refreshed view is not called. That is why you need to provide safeguards in **init\(\)** to prevent double initialization.
{% endhint %} -->

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/refresh.js)

## this.setParam\(\)

Use **this.setParam\(\)** method to set the URL related data. You can use **setParam\(\)** to change a URL segment or a URL parameter.

**Parameters:**

* the _name_ of the URL parameter,
* the new _value_,
* \(optional\) _display_, if it is set to _true_, the parameter is displayed in the URL.

For example, this is how you can change a URL parameter:

```javascript
this.setParam("mode", "12", true); // some?mode=12
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

## this.show\(\)

This method is used for in-app navigation. It loads view modules according to the path specified as a parameter.

**Parameters**:

- **path** (string, object) - the path to the module or a combination of parent-child modules with URL parameters or just URL parameters as an object,
- **config** (object) - the configuration with the [**target** parameter](#optional-target-parameter).

**Returns**: a promise.

```javascript
// views/toolbar.js
import { JetView } from "webix-jet";
export default class ToolbarView extends JetView{
	config(){
		return {
			view: "toolbar",
			elements: [
				{
					view: "button", value: "Details",
					click: () => this.show("details")
				}
			]
		};
	}
}
```

### Passing URL Parameters

You can use the **show()** method to pass URL parameters. You can do this by passing an object with the parameter:

```javascript
// views/toolbar.js
const param = "42";
this.show({ param });
// /#!/toolbar?param=42
```

### Additional Actions

**show\(\)** returns a _promise_. This is useful, when you plan to do something after a subview is rendered.

```javascript
// views/toolbar.js
import { JetView } from "webix-jet";
export default class ToolbarView extends JetView{
	config(){
		return {
			view: "toolbar",
			elements: [
				{
					view: "button", value:"Details",
					click: () => {
						this.show("details").then(function(){
							/* do something */
						});
					}
				}
			]
		};
	}
}
```

### Optional _target_ Parameter

A view can have named subviews, which is necessary when a view has several dynamic subviews. To render a module inside the named dynamic subview, call **this.show\(\)** with the second parameter -- the name of the subview.

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
this.show("small", { target:"right" });
```

_"small"_ is the name of a subview that will be placed in the right column of the _big_ view.

The above code works the same as this:

```javascript
this.getSubView("right").show("small");
```

_getSubView()_ returns a subview by the name passed as a parameter.


#### Showing a Subview in a New Window

You can use the **target** parameter to show a view in a popup or a window:

```js
this.show("popup", { target:"_top" });
```

For more details on view navigation, [read the "Navigation" article](../part-i-basic-usage/in-app-navigation.md).

## this.getUrl\(\)

Use **this.getUrl\(\)** method to get the URL as an array of segments, each one is an object with three properties:

* _page_ \(the name of the segment as _string_\)
* _params_ \(an object with URL parameters\)
* _index_ \(the number of the segment starting from 1\)

```javascript
// views/some.js
import {JetView} from "webix-jet";
export default class SomeView extends JetView{
    config(){
        return {
            view:"button", value:"Show URL", click: () => {
                var url = this.getUrl();
				//	[{ page:"some", params:{}, index:1 }]
            }
        };
    }
}
```

If you call this method from a dynamic subview (as opposed to the top view and a static subview), the method will return only the segments starting from this subview segment.

```javascript
//	views/top.js
import {JetView} from "webix-jet";
export default class SomeView extends JetView{
    config(){
        return {
			cols:[
				{ template:"Yop view" },
				{ $subview:true }
			]
		};
	}
	init(){
		this.show("some");
		//	"Show Url" will return
		//	[{ page:"some", params:{}, index:1 }]
	}
}
```

## this.getUrlString()

Use this method to return the URL as a string:

```javascript
// views/some.js
import {JetView} from "webix-jet";

export default class SomeView extends JetView{
    config(){
        return {
            view:"button", value:"Show URL", click: () => {
                var url = this.getUrlString();
				// "/some"
            }
        };
    }
}
```

If you call this method from a dynamic subview (as opposed to the top view and a static subview), the method will return only the part of URL starting from this subview segment.

```javascript
//	views/top.js
import {JetView} from "webix-jet";
export default class SomeView extends JetView{
    config(){
        return {
			cols:[
				{ template:"Yop view" },
				{ $subview:true }
			]
		};
	}
	init(){
		this.show("some");
		//	"Show Url" will return "/some"
	}
}
```

## this.ui\(\)

**this.ui\(\)** call is equivalent to **webix.ui\(\)**. It creates a new instance of the view passed to it as a parameter. It is advised to use **this.ui()** with Jet applications instead of **webix.ui()**, because components created this way are destroyed together with the related Jet view, which prevents memory leaks.

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
	// ...
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
        return { template:"test" };
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
        this.win = this.ui(SubView,{ container:"here" });
    }
}
```

where _"here"_ is the ID of a _div_ element on the page. _container_ can be an ID or an HTML element, e.g.:

```javascript
this.win = this.ui(SubView,{ container:document.getElementById("here") });
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

