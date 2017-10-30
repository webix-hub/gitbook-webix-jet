# Inner Jet Events and Error Handling

Apart from using built-in means like plugins, you can use a number of inner events. You might need this for adding some functionality if the plugins aren't enough or for error handling and debugging.

### app:render

The event is triggered before each view of an app is rendered. You can use it to change the UI config that you defined and add properties to UI controls, for instance, if you want to disable a button for some users.

```js
app.attachEvent("app:render", function(view,url,result){
    //...
});
```

The event receives three parameters:

**view** - the view for which the event is called (this.$scope)
**url** - the URL as an array of URL elements
**result** - a wrapper object used to change the UI from the *app:render* event

### app:route

Handling the **app:route** event is equivalent to handling the **urlChange** of a class view. The event fires after navigation to a view. **app:route** is used by the menu plugin to highlight menu options according to the URL.

```js
app.attachEvent("app:route", function(url){
    //...
})
```

It receives one parameter - the URL as an array of URL elements.

### app:guard

The **app:guard** event is triggered before navigation to another view. One of the typical cases to use this event is to create a guard: block some views and redirect users somewhere else. The **app:guard** event is called by the *UnloadGuard* plugin. You can attach **app:guard** with:

```js
app.attachEvent("app:guard", function(url, view, nav){

})
```

The event handler receives three parameters:

- *url* - a string with the attempted URL
- *view* - the parent view that contains subviews that are being changed
- *nav* - an object that defines navigation to the next view

**nav** has three properties:

- *redirect* is the new URL; in the example above it's corrected by the guard
- *url* is the URL split into an array of views
- *confirm* is a promise that is resolved when the **app:guard** event is called

Suppose you have a layout with three views, one parent and two subviews (simple template views for the example). This is the parent view that has two buttons that call **show** to render subviews:

```js
//import subviews
import allowed1 from "allowed";
import blocked from "blocked";
export default class TopView extends JetView {
	config(){
		return {
			rows:[
				{ type:"wide",cols:[
						{ view:"form",  width: 200, rows:[
							{ view:"button", value:"Allowed page", click:() =>
								this.show("allowed") },
							{ view:"button", value:"Blocked page", click:() =>
								this.show("blocked") }
						]},
						{ $subview: true }
					]
			}]
};}}
```

One of the subviews is supposed to be blocked. Let's create a guard that will block it and redirect users to the *allowed* subview. Group the views into app and attach the **app:guard** event:

```js
const app = new JetApp({
	start:		"/top/blocked",
	views:{
		top:		TopView,
		allowed:	allowed,
		blocked:	blocked
	}
});
app.attachEvent("app:guard", function(url, view, nav){
	if (url.indexOf("/blocked") !== -1){
		nav.redirect = "/top/allowed";
	}
});
app.render();
```

[The demo is available on Github](https://github.com/webix-hub/jet-demos/blob/master/sources/guards.js).

<!-- SECOND DEMO ABOUT LEVELS -->

## Error handling

There are four events that can be used to handle errors.

### app:error

This is a common event for all errors. The errors are logged if you set the **debug** property in your app config:

```js
var app = new JetApp({
    debug: true // console.log and debugger on error
});
```

You can also do something else, for example, show an error message in an alert box:

```js
app.attachEvent("app:error", function(err){
    alert("Error");
});
```

The event takes one parameter - the error object.

### Younger Siblings of *app:error*

Besides the common error event, there are three events for specific error types.

#### Useful for end-users

**app:error:resolve** fires when Jet can't find a module by its name. If this happens, it would be useful to redirect users somewhere else instead of showing them an empty screen:

```js
app.attachEvent("app:error:resolve", function(err, url) {
    webix.delay(() => app.show("/some"));
});
```

**app:error:resolve** receives two parameters:

- **err** - an error object
- **url** - the URL

#### Useful for developers:

These error events are more useful for developers as they inform about errors related to the UI.

###### app:error:render

This event is triggered on errors during view rendering, mostly Webix UI related. It means that some view UI config has been written incorrectly.

```js
app.attachEvent("app:error:render", function(err){
    alert("Check UI config");
});
```

###### app:error:initview

This event is triggered in case of an error during view rendering, mostly Webix Jet related. It means that Jet, while rendering Webix UIs, was unable to render the app UI correctly.

```js
app.attachEvent("app:error:initview", function(err,view){
    alert("Jet got lost");
})
```

The event takes two parameters:

- **err** - the error object
- **view** - the view that caused the error
