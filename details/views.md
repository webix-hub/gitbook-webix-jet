# <span id="contents">JetView API</span>

The JetView class has the following methods:

| Method                                   | Use it to                                     |
|------------------------------------------|-----------------------------------------------|
| [on(app,"event:name",handler)](#on)      | attach an event                               |
| [getParam(name, anywhere)](#getparam)    | give the API access to the URL parameters     |
| [getParentView()](#getpar)               | call methods of a parent view                 |
| [getRoot()](#getroot)                    | call methods of the Webix widget              |
| [getSubView(name)](#getsub)              | call the methods of a subview                 |
| [getUrl()](#url)                         | get the URL segment related to a view         |
| [setParam(name, value)](#setparam)       | set the URL parameters                        |
| [show("path")](#show)                    | show a view or a subview                      |
| [ui(view)](#ui)                          | create a popup or a window                    |
| [use(plugin, config)](#use)              | switch on a plugin                            |
| [\$\$("controlID")](#id)                 | return Webix widgets inside a view            |

-------------

### [<span id="use">this.use(plugin, config) &uarr;</span>](#contents)

The method launches a plugin. For example, this is how the **Status** plugin can be added:

```js
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

-----------

### [<span id="show">this.show("path") &uarr;</span>](#contents)

This method is used for in-app navigation. It loads view modules according to the path specified as a parameter:

```js
// views/toolbar.js */
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

**show()** returns a *promise*. This is useful, when you plan to do something after a subview is rendered.

```js
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

##### Optional *target* Parameter

If a view has several _$subview:true_ placeholders, you might want to show a subview instead of a certain placeholder. **this.show()** has an optional parameter that will make it possible. You must give the subview placeholder a name and pass this name to **this.show()**:

```js
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

*small* is the name of a subview that will be placed in the right column of the _big_ view.

The above code works the same as this:

```js
this.getSubView("right").show("small");
```
_getSubView_ returns a subview by the name passed as a parameter.

For more details on view navigation, [read the "Navigation" article](../basic/navigation.md).

---------

### [<span id="ui">this.ui(view,[container]) &uarr;</span>](#contents)

**this.ui()** call is equivalent to **webix.ui()**. It creates a new instance of the view passed to it as a parameter. For example, you can create views inside popups or modal windows with **this.ui()**. The good thing about this way is that it correctly destroys the window or popup when its parent view is destroyed.

<span id="window_contents">Details:</span>

- [*this.ui()* for Windows and Popups](#windows)
- [*this.ui()* with a Webix widget config](#widget)
- [*this.ui()* with Jet views inside a Webix layout](#jet_view)
- [optional *container* parameter](#container)

#### [<span id="windows">*this.ui()* for Windows and Popups &uarr;</span>](#window_contents)

This is a simple example of a Jet class with a window. The class has a method that can be used any other class view to show the window.

```js
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

Have a look at the parent view that instantiates the window with its **ui()** method:

```js
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

For more details about popups and windows, [go to the "popups and Windows" section](popups.md).

In the example above, a Jet view was passed to **this.ui()**, so the return value of **this.ui()** was also a Jet view. You can also pass Webix widgets or Jet views wrapped inside a Webix layout to the method.

#### [<span id="widget">*this.ui* with a Webix widget config &uarr;</span>](#window_contents)

*this.ui()* can return a Webix UI object:

```js
// views/top.js
import {JetView} from "webix-jet";

export default class TopView extends JetView {
  ...
  init(){
  	this.win = this.ui({ template:"test" });
  }
}
```

#### [<span id="jet_view">*this.ui* with Jet views inside a Webix layout &uarr;</span>](#window_contents)

*this.ui()* can also return a Webix UI object with Jet views inside:

```js
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

##### [<span id="container">Optional *container* Parameter &uarr;</span>](#ui)

**this.ui()** has an optional parameter - *container*. If you provide it, the view will be rendered inside the container:

```js
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

where *"here"* is the ID of a *div* element on the page. *container* can be an ID or an HTML element, e.g.:

```js
this.win = this.ui(SubView,{container:document.getElementById("here")});
```

----

### [<span id="on">this.on(app,"app:event:name",handler) &uarr;</span>](#contents)

Use **this.on()** to attach events. This way of attaching an event is convenient, because it automatically detaches the event when the view that called it is destroyed. This helps to avoid memory leaks that may happen, especially in older browsers.

```js
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

For more details on attaching and calling events, read the ["Events and Methods" section](events.md).

---

### [<span id="getroot">this.getRoot() &uarr;</span>](#contents)

Use **this.getRoot()** to call methods of a Webix widget inside a Jet class view.

```js
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

For more details on referencing views, [read the "Referencing" section](referencing.md).

---

### [<span id="getsub">this.getSubView(name) &uarr;</span>](#contents)

Use **this.getSubView()** if you want to get to the methods of a subview. It looks for a subview by its name, which you will have to give to the subview. You can do it like this:

```js
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

After you set the name to a subview, you can refer to it with **this.getSubView(name)** from the methods of the parent:

```js
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

For more details on referencing views, [read the "Referencing" section](referencing.md).

---

### [<span id="getpar">this.getParentView() &uarr;</span>](#contents)

Use **this.getParentview()** to get to the methods of the parent view. In this example, the child refers to its parent view with **this.getParentView()** and calls its **getSelected()** method:

```js
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

For more details, [read the "Referencing" section](referencing.md).

---

### [<span id="id">this.\$\$("controlID") &uarr;</span>](#contents)

Use **this.$$()** to look for Webix widgets by their local IDs inside the current Jet view<sup><a href="#footnote1" id="origin1">*</a></sup>.

```js
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

For details, [read the "Referencing" section](referencing.md).

---

### [<span id="url">this.getUrl() &uarr;</span>](#contents)

Use **this.getUrl()** method to get the URL as an array of segments, each one is an object with three properties:
- *page* (the name of the segment as *string*)
- *params* (an object with URL parameters)
- *index* (the number of the segment starting from 1)

For example, you can get the URL segment related to the view by accessing the *page* property:

```js
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

---

### [<span id="getparam">this.getParam(name, [parent]) &uarr;</span>](#contents)

Use **this.getParam()** method to get the URL related data. **getParam()** lets the API access the URL parameters (variables), including those of the parent view. This can be useful as views and subviews quite often share a common parameter.

**getParam()** takes two parameters:

- *name* - (mandatory, string) the name of the parameter;
- *parent* - (optional, Boolean) *false* by default; if *false*, it looks among those parameters that belong to the view that called the method; if *true*, it looks for parameters of the parent views.

**Details**

Consider a simple example with two views, a parent **page** and its subview **some**. The URL is:

```
#!/top/page?id=12/some
```

Where **id** is the parameter of the parent **page**.

This is how you can get **id** from the **page** view:

```js
//from page.js
var id = this.getParam("id"); //id == 12
```

And this is how you can get **id** from **some**:

```js
//from some.js
var id = this.getParam("id");       //id == ""
var id = this.getParam("id", true); //id == 12
```

For example, this is a parent view that puts the parameter into the URL and loads a subview:

```js
// views/details.js
import {JetView} from "webix-jet";

export default class DetailsView extends JetView {
	config(){
        return { cols:[
            { $subview:true }
        ]};
    }
    init(){
        this.show("?id=1/sub");
    }
}
```

Here **sub** is a Jet view that will access the parent's parameter and display it:

```js
// views/sub.js
import {JetView} from "webix-jet";

export default class sub extends JetView {
	config(){
		return {
			view:"template"
		};
	}
	urlChange(view){
		var id = this.getParam("id", true);
		view.setHTML("id="+id);
	}
}
```

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

---

### [<span id="setparam">this.setParam(name, value, [url]) &uarr;</span>](#contents)

Use **this.setParam()** method to set the URL related data. You can use **setParam()** to change a URL segment or a URL parameter:

```js
view.setParam("mode", "12", true); // some?mode=12
```

**setParam()** requires two obligatory parameters:
- the *name* of the URL parameter
- the new *value*.

The third parameter is optional and if set to *true*, the parameter is displayed in the URL.

**Details**

For example, this is how you can change a URL parameter with a widget inside a Jet view:

```js
// views/top.js
import {JetView} from "webix-jet";

export default class TopView extends JetView {
    config(){
        return {
            rows:[
                { view:"segmented", options:["full", "brief"], on:{
                    onChange: function(){
                        this.$scope.setParam("mode", this.getValue(), true);
                    }
                }}
            ]
        };
    }
}
```

**mode** will be set to either *full* or *brief* and the value will be displayed in the URL.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

[<span style="float:right;">**Back to top &uarr;**</span>](#contents)

- - -
<a id="footnote1" href="#origin1">* &uarr;</a>:
Starting with Webix Jet 1.5. If you want to look for widgets in the current view and all its subviews, use [this.getRoot().queryView()](https://docs.webix.com/api__ui.baseview_queryview.html).
