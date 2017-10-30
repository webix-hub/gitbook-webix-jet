# Jet Recipes

In this section you'll find elegant solutions for some common tasks.

## Access Guard - View Level

**Task**: You want to create views with several levels of access for different groups of users.

**Solution**: Just add a check in config.

For example, you have two user groups: *readers* and *writers*. You want to display some of the app contents depending on the group. This is the UI component with contents that you want to show to *readers*:

```js
export default class limited extends JetView{
	config(){
		return { 
        view:"form", rows:[
            { label:"Name", view:"text" },
            { label:"Email", view:"text" },
            {}
        ]};
	}
}
```

And this is the version of the same component you want to show to *writers*:

```js
export default class limited extends JetView{
	config(){
		return { 
            view:"form", rows:[
			{ label:"Name", view:"text" },
            { label:"Email", view:"text" },
            { view:"text", label:"Salary" },
            { view:"button", value:"Delete" },
            {}
		]};
	}
}
```

The solution is to create one common view and to *configure* its UI depending on the user group. The user group of the current group will be stored in the app config. Let's add a property with a default group name into the app config:

```js
import limited from "views/limited";
const app = new JetApp({
    access:		"reader",
    start:		"/top/limited",
    //...
});
```

*top* is a view with a side menu and a toolbar that includes *limited* as its subview. Next, let's define the UI for **limited** for default users (*readers*):

```js
class limited extends JetView{
	config(){
		var ui = { view:"form", rows:[
			{ label:"Name", view:"text" },
            { label:"Email", view:"text" },
            {}
		]};

		return ui;
	}
}
```

Next, let's add a check in config and change the view config for *writers*:

```js
class limited extends JetView{
	config(){
		var ui = { view:"form", rows:[
			{ label:"Name", view:"text" },
			{ label:"Email", view:"text" }
		]};

		if (this.app.config.access == "writer"){
			ui.rows.push({ view:"text", label:"Salary" });
			ui.rows.push({ view:"button", value:"Delete" });
		}

		ui.rows.push({});

		return ui;
	}
}
```

*this.app.config.access* gets to the access level of the current user. If you use [the **User** plugin](plugins.md#user), you can get the user name with *getUser* and check the group. For the demo, the user group is changed by a control in *top*:

```js
export class TopView extends JetView {
	config(){
		return {
			type:"space", rows:[
				{ view:"toolbar", cols:[
					{ view:"segmented", label:"Access Level",
						value:this.app.config.access,
						options:["reader","writer"], click:function(){
							// change access level, for demo only 
							var app = this.$scope.app;
							app.config.access = this.getValue();
							webix.delay(function(){
								app.refresh();
							});
					}}
				]},
				{ $subview: true }]
	};}
}
```

*webix.delay* calls **refresh** in 1 ms to ensure that the config has been reset before repainting the app. 

Here's an example how to block a view for *readers*: 

```js
export default class blocked extends JetView{
	config(){
		if (this.app.config.access != "writer"){
			return { };
		}

		return { template:"As writer you can read this content" };
	}
}
```

You can show this view via *top/blocked*.

[Check out this solution on GitHub](https://github.com/webix-hub/jet-demos/blob/master/sources/viewguard.js).

## Access Guard -- App Level

**Task**: You want to create views with several levels of access for different groups of users.

**Solution**: use **app:guard** event.

The **app:guard** event is triggered before navigation to another view. Here's how you can attach **app:guard**:

```js
app.attachEvent("app:guard", function(url, view, nav){
    if (url.indexOf("/blocked") !== -1)
        nav.redirect="/somewhere/else";
})
```

The event handler receives three parameters:

- *url* - a string with the attempted URL
- *view* - the parent view that contains a subview from the URL
- *nav* - an object that defines navigation to the next view

**nav** has three properties:

- *redirect* is the new URL; in the example above it's corrected by the guard
- *url* is the URL split into an array of URL elements
- *confirm* is a promise that is resolved when the **app:guard** event is called

Here's how you can block a view and redirect users to some *allowed* subview:

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

[Check out the solution on Github](https://github.com/webix-hub/jet-demos/blob/master/sources/appguard.js).

## Responsive UI - Sizing UI to Device
			
- responsive !
    - partly from the box ( Webix UI)
    - webix jet - just add check in config 
    
[Check out the solution on GitHub](https://github.com/webix-hub/jet-demos/blob/master/sources/screensize.js)