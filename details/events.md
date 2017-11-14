# Views Communication

Views are separated, but there should be some means of communication between them.

### Parameters

You can enable views communications with *parameters*. For instance, you need to open a form with some specific data from another view. Pass the needed parameters to *view.show*:

```js
// views/data.js
export default class DataView extends JetView{
    config(){
        return { rows:[
            { $subview:true }
        ]};
    }
    init(){
        this.show("./form?id=1");
    }
}
```

And here's **form**:

```js
// views/form.js
import { getData } from "../models/records";

export default class FormView extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" },
                { view:"text", name:"email" }
            ]
        };
    }
    urlChange(view,url){
        if(url[0].params.id){
            this.getRoot().setValues( getData(id) );
        }
    }
}
```

### Events

Feel free to use the in-app event bus for that. You can attach an event handler to the event bus in one view and trigger the event in another view.

First, attach an event to a Jet view:

```js
export default class FormView extends JetView{
    init(){
        this.app.attachEvent("save:form", function(){
            this.show("aftersave");
        });
    }
}
```

One more way to attach event is **this.on**. This way is better because it automatically detaches the event when the view that called it is destroyed.

```js
export default class FormView extends JetView{
    init(){
        this.on(this.app, "save:form", function(){
            this.show("aftersave");
        });
    }
}
```

Some other view can trigger the event, e.g.:

```js
export default class DataView extends JetView{
    config(){
        return {
            view:"button", click:() => {
                this.app.callEvent("save:form");
            }
        }
    }
}
```

### Declaring and Calling Methods

One more effective way of connecting views is methods. In one of the views we define a handler that will call some function, and in another view we call this handler.

Unlike events, methods both call actions in views and are able to return something useful. However, this option can only be used when we know that a view with the necessary method exists. It's better to use this variant with a parent and a child views. A method is declared in the child view and is called in the parent one.

##### Events vs Methods

Have a look at the example. Here's a view that has a method *setMode("mode")*:

```js
/* sources/views/child.js */
export default class ChildView extends JetView{
    cofig(){
        return {
            { view:"spreadsheet" }
        }
    }
    setMode("mode"){
        //sets the mode of the view
    }
}
```

And here's a parent view that will enclose *SubView*:

```js
/* sources/views/parent.js */
import child from "child"
export default class ParentView extends JetView{
    config() {
        return {
            rows:[
                { view:"button", value:"Set mode", click:() => {
                    this.getSubView().setMode("readonly")}
                }, 
                { subview: child }]
    }}
}
```

**this.getSubview()** refers to *child* and calls the method. It can take a parameter with the name of a subview if there are several subviews.

You can use methods for view communication in similar use-cases, but still events are more advisable here. Now let's have a look at the example where methods are better.

##### Methods vs Events

Suppose you want to create a file manager resembling Total Commander. The parent view will have two file views as subviews:

```js
config() { 
    return { 
        cols:[ 
            { name:"left", $subview:FileView }, 
            { name:"right", $subview:FileView }
        ]
}}
```

Here each subview has a name. *FileView* has the *loadFiles* method. Next, let's tell the file manager which paths to open in each file view:

```js
init() {
	this.getSubview("left").loadFiles("a");
	this.getSubview("right").loadFiles("b");
}
```