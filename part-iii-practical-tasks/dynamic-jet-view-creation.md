# Dynamic Jet View Creation

You can create apps that allow users build views themselves. New views will behave exactly as ordinary Jet views. They will be navigated to from the URL or with **show()** and loaded as subviews.

## View Locating Logic

You need a way to register newly-created classes so that the app could find them. Create a helper module that will be used to register new views. It will store the hash of view URL segments and the corresponding entities.

```js
// helpers/localviews.js
const localViews = {};  // { "someview" : SomeViewClass }
export default localViews;
```

Next you need to expand the default view creating logic and redefine the [view](part-ii-webix-jet-in-details/app-config.md#changing-view-creation-logic) setting of the JetApp configuration. The app will first look for a view among `localViews`. If it does not find anything there, the view will be located as usual, in the **views** folder.

```js
// myapp.js
import {JetApp} from "webix-jet";
import localViews from "helpers/localviews";
export default class MyApp extends JetApp{
	constructor(config){
		const defaults = {
            start:"/top/start",
            views:function(name){
                return localViews[name] || name;
            }
		};

		super({ ...defaults, ...config });
	}
}
```

Now you can use either of the two ways to create views:

- a factory function that will return views,
- a class that will be the base for all new views.

## Jet View Factory

You can define a function that will create new views, e.g.:

```js
// helpers/viewfactory.js
import {JetView} from "webix-jet";
export function viewFactory(config){
    class NewPageView extends JetView {
        config(){
            return config;
        }
    };
  
    return NewPageView;
}
```

Now you can create and register a new view and later navigate to it with [view.show()](part-ii-webix-jet-in-details/jetview-api.md#this-show) / [app.show()](part-ii-webix-jet-in-details/jetapp-api.md#app-show):

```js
localViews["newpage1"] = viewFactory({ template: "New Page 1" });
this.show("newpage1");
```

[Live demo >>](https://snippet.webix.com/mgup06rx)

## Shared Classes for Customized Views

Define a base class for newly-added views. It must have a constructor and **config()**:

```js
// helpers/viewbase.js
import {JetView} from "webix-jet";
export default class ViewBase extends JetView {
    constructor(app, name, config){
        super(app, name);
        this._config = config;
    }
    config(){
        return this._config;
    }
}
```

Now you can create and register new views and work with them as with ordinary Jet views stored in files:

```js
localViews["newpage3"] = new ViewBase(this.app, "some3", {
    template:"New page 3"
});
```

[Live demo >>](https://snippet.webix.com/ixlrjzdi)
