## Subview

Apart from direct injection, there are two more options of creating subviews.

### 1. View Injection

You can inject views into other views. For example, here are three views:

```js
/* views/myview.js */
export class MyView extends JetView {
    config() => { template:"MyView text" };
}
/* views/details.js */
export const Details = { 
    cols: [
        { template:"Details text" },
        { $subview:true } 
    ]    
};
/* views/form.js */
export const Form = () => {
    view:"form", elements:[
        { view:"text", name:"email", required:true, label:"Email" },
        { view:"button", value:"save", click:() => this.show("Details") }
    ]
}
```

Let's group them into a bigger view:

```js
/* views/bigview.js */
import {MyView} from "myview"
import {Details} from "details"
import {Form} from "form"
const BigView = {
    rows:[
        MyView,
        { $subview:"/Details/Form" }
    ]
}
```

### 2. App Injection

App is a part of the whole application that implements some scenario and is quite autonomous. It can be a subview as well. Thus you can create high level appications.

```js
/* views/form.js */
export class FormView extends JetView {
    config() {
        return {
            view: "form",
            elements: [
                { view: "text", name: "email", required: true, label: "Email" },
                { view: "button", value: "save", click: () => this.show("Details") }
            ]
        };
    }
}
/* view/details.js */
export const DetailsView = () => ({
    template: "Data saved"
});
```

Let's group these views into an app module:

```js
/* views/app1.js */
import {FormView} from "form"
import {DetailsView} from "details"
export var app1 = new JetApp({
    start: "/Form",
    router: JetApp.routers.EmptyRouter,
    views: {
        "Form": FormView,
        "Details": DetailsView
    }
});
```

Note that this app module isn't rendered. Next, the app is included into another view:

```js
/* views/page.js */
import {app1} from "app1"
export const PageView = () => ({
    rows: [ Toolbar, app1 ]
});
```

Finally, the view can be put into an app and rendered:

```js
/* app2.js */
import {PageView} from "page"
var app2 = new JetApp({
    start: "/Page",
    router: JetApp.routers.HashRouter,
    views: {
        "Page": PageView
    }
}).render();
```

As a result, this is a two-level app. [Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/06_highlevel.html).

