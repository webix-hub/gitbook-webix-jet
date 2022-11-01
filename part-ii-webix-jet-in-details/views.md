# Creating Views

A view is a part of the UI that is large enough to be kept separately: a form, a datatable with the related toolbar, a navigation menu, etc. Views can be defined in three ways, the best being a ES6 class.

## Class Views

Views can be defined as ES6 classes that inherit from the _JetView_ class.

```javascript
// views/top.js
import {JetView} from "webix-jet";
export default class TopView extends JetView {
    config(){
        return { cols:[
            { view:"menu" }
            { template:"Something here" }
        ]};
    }
}
```

### Advantages of Classes

* Views defined as classes are **dynamic** and each new instance can be changed when it's created.
* View classes have [init\(\)](../api/jetview-handlers.md#init) and other **lifetime methods** that can be redefined by users.
* You can also define **custom methods** and **local variables**.
* All instances have their individual **inner states**. E.g. if you use the same Toolbar class to add identical toolbars at the top and at the bottom, there are two instances of the Toolbar class and the toolbars will behave independently.
* Classes have the **this** pointer that references the view inside methods and handlers.
* You can **extend** class views. Views can inherit not only from the JetView class, but from each other. For details about code reuse, read section ["Creating Similar Views"](views.md#creating-similar-views) in this chapter.

### JetView Constructor

You can create new instances of Jet class views with a constructor. This is very useful if you want to reuse a view several times, but want each instance to be different in some way \(changes in UI, different data\).

```javascript
// views/customerdata.js
import {JetView} from "webix-jet";
export default class CustomersData extends JetView{
    constructor(app, data){
        super(app);
        this._componentData = data;
    }
    config(){
        return {
            view:"datatable",
            columns:[
                { id:"name", header:["Name", {content:"textFilter"} ], sort:"text", fillspace:true },
                { id:"email", header:"Email", sort:"text", adjust:"data" },
                { id:"phone", header:"Phone", sort:"text", width:120 }
            ]
        };
    }
    init(view){
        view.parse(this._componentData);
    }
}
```

Then you can create a new instance of CustomerData in [config\(\)](../api/jetview-handlers.md#config) of another Jet view:

```javascript
// views/customers.js
import {JetView} from "webix-jet";
import {getRecords} from "models/orders";
import {getClients} from "models/customers";

export default class TopView extends JetView{
    config(){
        return {
            rows:[
                new CustomersData(this.app,"",getRecords()),
                new CustomersData(this.app,"",getClients())
            ]
        };
    }
}
```

### JetView Methods

Webix UI life-time handlers are implemented through **JetView** class methods. Here are the methods that you can redefine:

* [config\(\)](../api/jetview-handlers.md#config)
* [init\(\)](../api/jetview-handlers.md#init-view-url)
* [urlChange\(\)](../api/jetview-handlers.md#urlchange-view-url)
* [ready\(\)](../api/jetview-handlers.md#ready-view-url)
* [destroy\(\)](../api/jetview-handlers.md#destroy)

Life-time handlers are called in this specific order (as in the list above). When you deal with [views and subviews](subviews.md), this order for a parent view is 'broken' by life-time handlers of the subview. For instance, if the URL is changed to _a/b_, the order in which view class methods are called is the following:

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

[Test it >>](https://snippet.webix.com/rqfbv23k)

[JetView also has other methods](../api/jetview-methods.md).

### Creating Similar Views

ES6 inheritance can help you reuse components for creating slightly different ones. If views share many common traits, you can create a base view and then create a necessary number of subclasses, in which you can redefine necessary parts of UI/logic, load different data, etc.

To achieve this, you can define a custom base class and use it instead of _JetView_ for creating new views.

For example, if you need to create a lot of similar datatables, you can define a class that will store all the common elements \(configuration handlers, logic, etc.\):

```javascript
// views/basedatatable.js
import {JetView} from "webix-jet";
export default class BaseDatatable extends JetView {
    constructor(app, config){
        super(app);
        this.grid_config = config;
    }
    config(){
        return { view:"datatable", columns: this.grid_config.columns };
    }
}
```

Next you can create custom datatable views, each one can define parameters for the base class and redefine any of its methods if necessary:

```javascript
// views/products.js
import BaseDatatable from "views/basedatatable";
import products from "models/products"; //data collection
export default class ProductsView extends BaseDatatable {
    constructor(app){
        super(app, {
            columns:[
                {id:"id",header:""},
                {id:"product",header:"Product"},
                {id:"stock",header:"In stock"}
            ]
        });
    }
    init(view){
        view.parse(products);
    }
}
```

### Local Methods and Properties

You can define class view methods and properties. **this** inside methods refers to the instance of the corresponding view class.

Consider a simple example. The class has a counter stored as a class property, which is declared in [init\(\)](../api/jetview-handlers.md#init). There is also a method that increments it, when a button is clicked.

```javascript
import {JetView} from "webix-jet";
export default class Toolbar extends JetView {
    config(){
        return {
            view:"button", value:"Click me",
            click:() => this.doClick("Clicked")
        };
    }
    init(){
        this._counter = 0;
    }
    doClick(message){
        this._counter++;
        webix.message(message+" "+this._counter);
    }
}
```

## Simple Views

Views can be created as pure objects.

```javascript
/* views/list.js */
export default {
    view:"list"
}
```

or

```javascript
const list = {
    view:"list"
};
export default list;
```

### Advantage

* This is a dead simple way to create a view.

### Disadvantages

* Simple views are static and are included as they are.
* Simple views **do not have** [init\(\)](../api/jetview-handlers.md#init) and other methods that JetView has.

## Object "Factory Pattern"

View objects can also be returned by a factory function.

```javascript
/* views/details.js */
export default () => {
    const data = [];
    for (let i = 0; i < 10; i++)
        data.push({ value:i });

    return {
        view:"list", options:data
    };
}
```

### Advantages

* Such views are still simple.
* Such views are dynamic.

### Disadvantages

* Such views **do not have** [init\(\)](../api/jetview-handlers.md#init) or other methods that classes have.

## ❓ Which Way to Define Views is Better

All ways provide nearly the same result.

When you are using the **"class"** approach, you can define the UI configuration and methods for lifetime handlers.

When you are using the **"factory function"** approach, you can define a dynamic UI config without lifetime handlers.

When you are defining views as **simple view objects**, you can define UI config only.

So if you are choosing between **classes** and **objects**, it is flexibility VS brevity.

If you are not sure which one to use, use classes. A class with the [config\(\)](../api/jetview-handlers.md#config) method works exactly the same as the **const** declaration.

