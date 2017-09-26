## (Detailed) Plugins

New version of Webix Jet provides both predefined plugins and the ability to create your own. This is the syntax to use a plugin:

~~~js
this.use(JetApp.plugins.PluginName, {
    id:"controlId", urls:{/*...*/}
});
~~~

After the plugin name you are to specify the configuration of the plugin.

### 1. Default Plugins

#### Menu Plugin

This plugin simplifies you life if you plan to create a menu. The plugin sets URLs for menu option, buttons or other controls you plan to use for showing subviews. Also there's no need to provide handlers to restore the state of the menu on page reload or URL change. The right menu item is highlighted automatically.

<!-- calls getValue(): didn't work with list as a menu, probably because of it -->

Let's create a familiar toolbar with a segmented button and use the plugin:

~~~js
class ToolbarView extends JetView{
	config(){
		return { 
			view:"toolbar", elements:[
				{ view:"label", label:"Demo" },
				{ view:"segmented", localId:"control", options:[
					"Details",
					"Dash"
				]}
			]
		};
	}
	init(ui, url){
		this.use(JetApp.plugins.Menu, {
			id:"control"
		});
	}
}
~~~

The plugin config contains the ID of the view element that's going to serve as a menu. If you click the buttons and reload the page, the app will behave as expected. The **menu** plugin has one more good part. You can change the URLs for every menu item. Let's add two more buttons and set URLs for them in the plugin config:

~~~js
class ToolbarView extends JetView {
	config(){
		return { 
			view:"toolbar", elements:[
				{ view:"label", label:"Demo" },
				{ view:"segmented", localId:"control", options:[
					"Details",
					"Dash", 
					"Demo",
					"Other"
				]}
			]
		};
	}
	init(ui, url){
		this.use(JetApp.plugins.Menu, {
			id:"control",
			urls:{
				Demo  : "Demo/Dash",
				Other : "Demo/Details"
			}
		});
	}
}
~~~

[Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/04_plugins.html[[](https://github.com/webix-hub/jet-core/blob/master/samples/05_guard.html](https://github.com/webix-hub/jet-core/blob/master/samples/05_guard.html))).

#### UnloadGuard Plugin

The Unload plugin can be used to prevent users form leaving the view on some conditions. For example, this can be useful in the case of forms with unsaved data. The plugin can intercepts the event of leaving the current view and going to the next and, e.g. show the *are you sure* dialogue. Besides, it can be used for input validation (details in 05_referencing).

Have a look at such a form:

~~~js
/* views/form.js */
class FormView extends JetView{
	config(){
		return { 
			view:"form", elements:[
				{ view:"text", name:"email", required:true, label:"Email" },
				{ view:"button", value:"save", click:() => this.show("Details") }
			]
		};
	}
	
	init(ui, url){
		this.use(JetApp.plugins.UnloadGuard, () => {
			if (this.getRoot().validate())
				return true;

			return new Promise((res, rej) => {
				webix.confirm({
					text: "Are you sure ?",
					callback: a => a ? res() : rej()
				})
			});
		});
	}
}
~~~

[Check out the demo](https://git.webix.io/mkozhukh/wjet/src/master/samples/05_guard.html).

#### Login Plugin

    - login through custom script
    - login with external OATH service ( Google, Github, etc. )

(?)
#### Session Plugin
#### Theme plugin
#### Locale plugin

2. Custom Plugins

(?)