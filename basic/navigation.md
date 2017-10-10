## Navigation

Navigation is implemented by changing the URL of the page. Only the part after the hashbang \(\#!\) is changed[^1]. The framework reacts to the URL change and rebuilds the interface based on the URL. In the previous section, you've read about direct URL navigation. There are three more ways to manipulate views and subviews.

The URL of the page reflects current state of the app. By default, it is stored as part after hashband. When we chaging the url, app updates self accordingly. 


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

The current URL is _"/layout/details"_, so the subview is **details**. If you want to rebuild the whole app and load the **details** view as the only view, specify the name of the view with a slash:

```js
/* toolbar.js*/
...
{ view:"button", value:"Details", click: () => {
    this.show("/details");
}}
...
```

To replace the current subview with a different one, specify the name as it is or with *./*:

```js
{ view:"button", value:"demo", click: () => {
    this.show("demo");
}}

//or

{ view:"button", value:"demo", click: () => {
    this.show("./demo");
}}
```

The resulting URL is going to be */layout/demo*.

URL syntax resembles the way you navigate through directories. So you can move some levels up, for example:

```js
{ view:"button", value:"demo", click: () => {
    this.show("../../bigview");
}}
```

As a result, the app URL will be */bigview*.

[Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/02_life_stages.html) to see how the described ways of navigation are used in an application.

This is all about Webix Jet in a nutshell. For more details, go on to the next chapter.

<!-- footnotes -->
[^1]:
This is relevant for HashRouter, which is the default router. There is no hashbang if you use UrlRouter. The app URL isn't displayed at all if you use other types of routers. However, the app URL is stored for all the three routers except EmptyRouter and the behavior is the same as if the URL were displayed.  for more details, [see the section on routers](../details/routers.md).