## Navigation

Navigation is implemented by changing the URL of the page. Only the part after the hashbang \(\#!\) is changed. The framework reacts to the URL change and rebuilds the interface from the elements after the hashbang. In the previous section you've read about direct URL navigation. There are three more ways to manipulate views and subviews.

### 1. Jet Links

You can add links with the **route** attribute instead of the usual **href** and provide the URL to the desired views, e.g.:

```html
<a route="/Details/Data"></a>
```

After you click on the link, the app UI will be rebuilt and will consist of a main view _Details_ and a subview _Data_.

### 2. app.show\(\)

The **app.show\(\)** method is applied to the whole application. You can call the method from control handlers, for instance.

```js
{ view:"button", value:"Details", click: () => {
    this.app.show("/Demo/"+this.getValue())
}}
```

After a button click, the URL will change, and the whole app will be rebuilt according to it.

### 3. view.show\(\)

You can also change the URL by calling the **show\(\)** method from a specific view. A specific instance of the related view class is referenced with **this.$scope**. This way gives you more freedom, as it allows rebuilding only this view or only its subview, not the whole app. For example, suppose you have a view like this:

```js
[
    Layout,
    { $subview:true }
]
```

The current URL is _"/Layout/Toolbar"_, so the subview is **Toolbar**. If you want to rebuild the whole app and load the **Toolbar** view as the only view, specify the name of the view with a slash:

```js
/* toolbar.js*/
{ view:"segmented", localId:"control", options:["Toolbar", "Demo", "Task"], click: () => {
    var value = this.getValue();
    if (value === "Toolbar")
        this.$scope.show("/Toolbar");
    //other options
}},
```

If you want to reload the current view \(**Toolbar**\) with the **Demo** view, specify the name of the view without a slash. Thus the URL will change to _"/Layout/Demo"_.

```js
//second option
else if (value === "Demo")
    this.$scope.show("Demo")
```

Suppose Toolbar has a subview of its own. If you want to reload the subview with the Demo view, specify the parameter with "./":

```js
//third option
else if (value === "Task")
    this.$scope.show("./Demo")
```

The URL will change to _"/Layout/Toolbar/Demo"_.

[Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/02_life_stages.html) to see how the described ways of navigation are used in an application.

This is all about Webix Jet in a nutshell. For more details, go on to the next chapter.

