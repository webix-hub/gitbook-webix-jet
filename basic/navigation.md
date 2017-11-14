## Navigation (Routing)

Navigation is implemented by changing the URL of the page. Only the part after the hashbang \(\#!\) is changed<sup><a href="#myfootnote1" id="origin">1</a></sup>. The framework reacts to the URL change and rebuilds the interface based on the URL. 

The URL of the page reflects the current state of the app. By default, it is stored as a part after the hashbang. When we change the URL, the app updates itself accordingly. 

### Advantages of Jet Navigation

- *Browser Navigation Keys*: You can move backwards and forwards to previously opened subviews.
- *Refresh Friendly*: If you reload the page, the URL will stay the same and the state of the UI will be exactly the same as before page reload.
- *Convenient Development*: If you work on some particular subview (*aaa*), you can open it separately with URL like *#!/aaa* and test it.

In the previous section, you've read about direct URL navigation. There are three more ways to show views and subviews.

### 1. Jet Links

You can add links with the **route** attribute instead of the usual **href** and provide the URL to the desired views, e.g.:

```html
<a route="/details/data"></a>
```

After you click on the link, the app UI will be rebuilt and will consist of the main view _Details_ and a subview _Data_.

### 2. app.show\(\)

The **app.show\(\)** method is applied to the whole application. You can call the method from control handlers, for instance.

```js
{ view:"button", value:"Details", click: () => {
    this.app.show("/demo/"+this.getValue().toLowerCase());
}}
```

After a button click, the URL will change, and the app UI will be rebuilt according to it.

### 3. view.show\(\)

You can also change the URL by calling the **show\(\)** method from a specific view. A specific instance of the related view class is referenced with **this.$scope**. This way gives you more freedom, as it allows rebuilding only this view or only its subview, not the whole app or app module. For example, suppose you have a view like this:

```js
[
    Layout,
    { $subview:true }
]
```

The current URL is _"/layout/details"_, so the subview is **details**.To replace the current subview with a different one, specify the name as it is or with *"./"*:

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

 If you want to rebuild the whole app and load the **details** view as the only view, specify the name of the view with a slash:

```js
/* toolbar.js*/
...
{ view:"button", value:"Details", click: () => {
    this.show("/details");
}}
...
```

You can also read [a section on navigation](../details/navigation.md) in the advanced chapter.

This is all about Webix Jet in a nutshell. For more details, go on to the next chapter.

<!-- footnotes -->
- - -
<a id="myfootnote1" href="#origin">1</a>:
This is relevant for HashRouter, which is the default router. There is no hashbang if you use UrlRouter. The app URL isn't displayed at all if you use other types of routers. However, the app URL is stored for all the three routers except EmptyRouter and the behavior is the same as if the URL were displayed. For more details, [see the section on routers](../details/routers.md).