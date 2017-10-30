# Inner Jet Events and Error Handling

Apart from using built-in means like plugins, you can use a number of inner events. You might need this for adding some functionality if the plugins aren't enough or for error handling and debugging.

### app:render

The event is triggered before the app is rendered. You can use it to change the UI config that you defined and add properties to UI controls. For instance, if you want to disable a button for some users.

```js
app.attachEvent("app:render", function(){
    
})
```



### app:route

app.attachEvent("app:route") - ( urlChange ) after navigation (used by menu plugin, highlights controls according to URL)

### app:guard

The app:guard event is triggered before navigation to another view. One of the typical cases to use this event is to create a guard: block some views and redirect users somewhere else. The **app:guard** event is called by the *UnloadGuard* plugin. You can attach **app:guard** with:

```js
app.attachEvent("app:guard", callback)
```

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

**app:guard** fires when the URL is about to change. The event handler receives three parameters:

- *url* - a string with the attempted URL
- *view* - the parent view that contains subviews that are being changed
- *nav* - an object that defines navigation to the next view

**nav** has three properties:

- *redirect* is the new URL; in the example above it's corrected by the guard
- *url* is the URL split into an array of views
- *confirm* is a promise that is resolved when the **app:guard** event is called

[The demo is available on Github](https://github.com/webix-hub/jet-demos/blob/master/sources/guards.js).

<!-- SECOND DEMO ABOUT LEVELS -->

## Error handling

There are four events that are triggered on errors.

### app:error

app.attachEvent("app:error", function(err){			//all errors, common event
    alert("Error");
})

### Younger Siblings of *app:error*

Useful for end-users:

app.attachEvent("app:error:resolve") - can't find module by its name
app.attachEvent("app:error:resolve", (err, url) {
    webix.delay(() => app.show("/some"));			//instead of showing empty screen to users
});

Useful for developers:

app.attachEvent("app:error:render")
    - error during rendering view, mostly Webix UI related - some view written incorrectly
app.attachEvent("app:error:initview"
    - error during rendering view, mostly Webix Jet related - when jet, while rendering Webix UIs, got lost

## For Debugging

new JetView({		//JetApp??
    debug: true // console.log and debugger on error
})