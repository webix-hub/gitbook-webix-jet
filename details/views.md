## Views

After reading the first chapter of this guide, you are familiar with the concept of _view_. Now it's time to find out all the ways of creating views. You can create views in three ways.

### 1. Object Views

#### Pure Objects

Views can be created as objects.

* Disadvantages
  if you prefer OOP, the syntax gets convoluted

Here's a simple template object:

```js
/* views/dash.js */
const DashView = { template:"Dash" };
```

#### Object "Factory Pattern"

View objects can also be returned by a factory function.

* Advantages

//?

* Disadvantages

//?

Here's a simple template view returned by a factory:

```js
/* views/details.js */
const DetailsView = () => ({ template:"App" });
```

### 2. Class Views

Views can be defined as JS6 classes.

#### Advantages of classes

* Multiple instances

All instances have their individual states. E.g. if you use the same Toolbar class for to add identical toolbars, there are two instances of a Toolbar class and the toolbars will behave independently.

* **this**

The **this** pointer is a reference to the object inside methods and handlers.

* Inheritance

With JS6 classes, inheritance is closer to classic OOP and the syntax is nicer. Inheritance can help you reuse old components for creating slightly different ones. For example, if you already have a toolbar and want to create a similar one, but with one additional button, define a new class and inherite from the old toolbar.

* Data

Data collections can be created inside a method, if you need to process the data before loading and don't want to create additional modules for that.

#### JetView Methods

View classes inherit from **JetView**. Webix UI events are implemented through class methods. Here are the methods that you can define while defining your class views:

* config\(ui\)

This method returns the initial UI configuration of a view, all its contents.

```js
/* views/toolbar.js */
class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
}
```

The method receives one parameter -- the UI of the view.

* init\(view, url\)

The method is called only once for every instance of a view class when the view is rendered. It can be used to change the initial UI configuration of a view. For instance, the above defined toolbar will be always rendered with the first segment active regardless of the URL. You can link the control state to the URL:

```js
class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
    init(view, url){
        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
}
```

The method receives two parameters:

* the view UI
* the URL

* urlChange\(view,url\)

This method is called every time there's a change of views. It reacts to the change in the URL after **!\#**. **urlChange** is only called for the view that is rendered and for its parent. Consider the following example. The initial URL is:

```
/Layout/Demo/Details
```

If you change it to

```
/Layout/Demo/Preview
```

**urlChange** will be called for **Demo** and **Preview**. [Check out the demo](https://git.webix.io/mkozhukh/wjet/src/master/samples/02_life_stages.html).

The **urlChange** method can be used to restore the state of the view according to the URL, e.g to highlight the right menu item.

```js
class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
    init(view, url){
        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
    urlChange(view, url){
        if (url.length > 1)
            this.$$("control").setValue(url[1].page);
    }
}
```

The method receives two parameters:

* the view UI
* the URL

* destroy

**destroy** is called only once for each instance when the view is destroyed. The view is destroyed when the corresponding URL element is no longer present in the URL. **destroy** also can be used to destroy temporary objects like popups and other child components to prevent memory leaks.

```js
class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
    init(view, url){
        var popup = webix.ui({      //???
            view:"popup", 
            body:"Toolbar is created"
        });

        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
    urlChange(view, url){
        if (url.length > 1)
            this.$$("control").setValue(url[1].page);
    }
    destroy(){
        popup.destructor();
    }
}
```



