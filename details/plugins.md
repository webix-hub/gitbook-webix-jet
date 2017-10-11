# Plugins

The new version of Webix Jet provides both predefined plugins and the ability to create your own. This is the general syntax to use a plugin:

~~~js
this.use(JetApp.plugins.PluginName, {
    id:"controlId", /* config */
});
~~~

After the plugin name, you are to specify the ID of the control you want to use the plugin for and the configuration of the plugin if necessary.

## 1. Default Plugins

#### Menu Plugin

This plugin simplifies your life if you plan to create a menu. The plugin sets URLs for menu options, buttons or other controls you plan to use for showing subviews. Also, there's no need to provide handlers to restore the state of the menu on page reload or URL change. The right menu item is highlighted automatically.

Let's create a familiar toolbar with a segmented button and use the plugin:

~~~js
/* sources/views/toolbar.js */
export default class ToolbarView extends JetView{
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
				Demo  : "demo/dash",
				Other : "demo/details"
			}
		});
	}
}
~~~

[Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/04_plugins.html).

#### UnloadGuard Plugin

The **Unload** plugin can be used to prevent users from leaving the view on some conditions. For example, this can be useful in the case of forms with unsaved data. The plugin can intercept the event of leaving the current view and going to the next and, e.g. show the *are you sure* dialogue. Besides, it can be used for input validation.

The plugin reacts to an attempt of changing the URL. The syntax for using a plugin is _this.use\(plugin,handler\)_. Use takes two parameters:

* a plugin name
* a function that will handle the **Unload** event

You can move validation from the **Save** button handler to the plugin handler:

```js
class FormView extends JetView {
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
```

If the input isn't valid, the function returns a promise with a dialogue window. Depending on the answer, the promise either resolves and the Guard lets the user go to the next view, or it rejects. No pasaran.

[Check out the demo](https://github.com/webix-hub/jet-core/blob/master/samples/05_guard.html).

#### Login Plugin

- login through a custom script
- login with external OATH service ( Google, Github, etc. )

<!-- 
getUser(){
	return user;
},
getStatus(server? : boolean){
	if (!server)
		return user !== null;

	return model.status().catch(() => null).then(data => {
		user = data;
	});
},
login(name:string, pass:string){
	return model.login(name, pass).then((data) => {
		user = data;
		if (!data) throw("Access denied");

		app.show(afterLogin);
	});
},
logout(){
	user = null;
	return model.logout();
}

app.setService("user", service);
 -->

```js

```

## Theme plugin

<!-- getTheme(){ return theme; },
		setTheme(name:string, silent?:boolean){ -->

<!-- app.setService("theme", service);
	service.setTheme(theme, true); -->

<!-- ```js
import {JetApp, JetView, plugins} from "webix-jet";

export default class SettingsView extends JetView {
	config(){
		const _ = this.app.getService("locale")._;
		const lang = this.app.getService("locale").getLang();


		return {
			type:"space", rows:[
				{ template:_("Settings"), type:"header" },
				{ name:"lang", optionWidth: 120, view:"segmented", label:_("Language"), options:[
					{id:"en", value:"English"},
					{id:"es", value:"Spanish"}
				], click:() => this.toggleLanguage(), value:lang },
				{}
			]
		};
	}
	toggleLanguage(){
		const langs = this.app.getService("locale");
		const value = this.getRoot().queryView({ name:"lang" }).getValue();
		langs.setLang(value);
	}
}


webix.ready(() => {
	const app = new JetApp({
		id:			"plugins-themes",
		start:		"/start",
		views:{
			start: SettingsView
		}
	});
	app.use(plugins.Locale);
	app.render();
});
``` -->

## Locale plugin

<!-- _:null,
	polyglot: null,
	getLang(){ return lang; },
	setLang(name:string, silent? : boolean){
		let data = require("jet-locales/"+name);
		if (data.__esModule) {
			data = data.default;
		}

		const poly = service.polyglot = new Polyglot({ phrases:data });
		poly.locale(name);

		service._ = webix.bind(poly.t, poly);
		lang = name;

		if (storage){
			storage.put("lang", lang);
		}
		if (!silent){
			app.refresh();
		}
	}
};

app.setService("locale", service);
service.setLang(lang, true); -->

<!-- ```js
import {JetApp, JetView, plugins} from "webix-jet";

export default class SettingsView extends JetView {
	config(){
		const _ = this.app.getService("locale")._;
		const lang = this.app.getService("locale").getLang();


		return {
			type:"space", rows:[
				{ template:_("Settings"), type:"header" },
				{ name:"lang", optionWidth: 120, view:"segmented", label:_("Language"), options:[
					{id:"en", value:"English"},
					{id:"es", value:"Spanish"}
				], click:() => this.toggleLanguage(), value:lang },
				{}
			]
		};
	}
	toggleLanguage(){
		const langs = this.app.getService("locale");
		const value = this.getRoot().queryView({ name:"lang" }).getValue();
		langs.setLang(value);
	}
}


webix.ready(() => {
	const app = new JetApp({
		id:			"plugins-themes",
		start:		"/start",
		views:{
			start: SettingsView
		}
	});
	app.use(plugins.Locale);
	app.render();
});
``` -->

## Status plugin


<!-- const basetext = {
	"good":	"Ok",
	"error": "Error",
	"saving": "Connecting..."
};

app.setService("status", {
		getStatus,
		setStatus,
		track
	}); -->

<!-- ```js
import {JetApp, JetView, plugins} from "webix-jet";

export default class StartView extends JetView {
	config(){
		return {
			type:"space", rows:[
				{ template:"Some Data", type:"header" },
				{ view:"datatable", id:"table", autoConfig:true, editable:true },
				{ view:"template", id:"app:status", height: 30 }
			]
		};
	}
	init(){
		this.use(plugins.Status, { 
			target: "app:status",
			ajax:true,
			expire: 5000
		});

		const data = new webix.DataCollection({
			url:"/assets/data.json",
			save:"//docs.webix.com/wrongurl"
		});
		webix.$$("table").parse(data);
	}
}


webix.ready(() => {
	const app = new JetApp({
		id:			"plugins-themes",
		start:		"/start",
		views:{
			start: StartView
		}
	});

	app.render();

	app.attachEvent("app:error:server", function(){
		webix.alert({
			title:"Data Saving Error",
			width: 480,
			text:"This sample has not server side,<br> so any attempt to save data will result in an error."
		});
	});
});
``` -->

<!-- status
	- ajax:true, data, remote:true ( webix.remote )

theme
	flat-shady
		title:"flat"
		document.body.className += " .theme-flat-shady"
		webix.setSkin("flat")
 -->

**2.Custom Plugins**

You can define your own plugins.