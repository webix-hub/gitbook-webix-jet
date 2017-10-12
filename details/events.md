## Views Communication

### Events

Views are separated, but there should be some means of communication between them. Feel free to use the in-app event bus for that. You can attach an event handler to the event bus in one view and trigger the event in another view.

First, attach an event to one view:

```js
init(){
    on(app, "eventName", function(something){
        //handler
        this.show(something);
    });
}
```

In the other view, there should be code that triggers the event, e.g.:

```js
on:{
    onEvent:function(){
        app.callEvent("eventName", [some_value]);
    }
}
```

### Aliases

To make your life happier, there are aliases for methods used to trigger events.

**1.**_**trigger**_

Instead of using **callEvent**

```js
app.callEvent("eventName", [some_value]);
```

you can write

```js
app.trigger("eventName", [some_value]);
```

**2.**_**action**_

This alias unites both the click handler and the **callEvent** method. So the same code transforms into:

```js
on:{
    onEventName:app.action("eventName", [some_value]);
}
```

### Declaring and Calling Methods

One more effective way of connecting views is methods. In one of the views we define a handler that will call some function, and in another view we call this handler.

Unlike events, methods both call actions in views and are able to return something useful. However, this option can only be used when we know that a view with the necessary method exists. It's better to use this variant with a parent and a child views. A method is declared in the child view and is called in the parent one.

#### Events vs Methods

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

And here's a parent view than will enclose *SubView*:

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

#### Methods vs Events

Suppose you want to create a filemanager resembling TotalCommander. The parent view will have two file views as subviews:

```js
config() { 
    return { 
        cols:[ 
            { name:"left", $subview:FileView }, 
            { name:"right", $subview:FileView }
        ]
}}
```

Here each subview has a name. *FileView* has the *loadFiles* method. Next, let's tell the filemanager which paths to open in each file view:

```js
init() {
	this.getSubview("left").loadFiles("a");
	this.getSubview("right").loadFiles("b");
}
```