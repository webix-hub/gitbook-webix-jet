# Plugins

The new version of Webix Jet provides predefined plugins and the ability to create your own plugins.

## <span id="contents">1. Default Plugins</span>

**View Plugins**

These plugins are switched on to a specific view by *view.use*:

- [the Menu plugin](#menu)
- [the UnloadGuard plugin](#unload)
- [the Status plugin](#status)
- [the UrlParam plugin](#urlparam)

**App Plugins**

These plugins are switched on to the whole app with *app.use*:

- [the User plugin](#user)
- [the Theme plugin](#theme)
- [the Locale plugin](#locale)

### [<span id="menu">Menu Plugin &uarr;</span>](#contents)

This plugin simplifies your life if you plan to create a menu for dynamic subviews. The plugin sets URLs for menu options, buttons or other controls you plan to use for navigation. Also, there is no need to provide handlers to restore the state of the menu on page reload or URL change. The right menu item is highlighted automatically.

![](../images/top_data.png)

This is the general syntax to use the plugin:

~~~js
// views/some.js
...
init(){
	this.use(JetApp.plugins.PluginName, {
		id:"control_id"
	});
}
~~~

**this** in the **init** method refers to the instance of the Jet view class. After the plugin name, you must specify the local ID of the Webix control or widget that you want to use as a menu.

Let's create a toolbar with a segmented button and use the plugin for it:

~~~js
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView{
	config(){
		return { 
			view:"toolbar", elements:[
				{ view:"label", label:"Demo" },
				{ view:"segmented", localId:"control", options:[
					"details",
					"dash"
				]}
		]};
	}
	init(ui, url){
		this.use(JetApp.plugins.Menu, {
			id:"control"
		});
	}
}
~~~

The plugin config contains the local ID of the view element that is going to serve as a menu. If you click the buttons and reload the page, the app will behave as expected. The **Menu** plugin has another benefit as well. You can change the URLs for every menu item. This is useful if you want to display shortened names instead of long subview URLs.

Let's set URLs for the buttons in the plugin config:

~~~js
// views/toolbar.js
import {JetView} from "webix-jet";

export default class ToolbarView extends JetView {
	config(){
		return { 
			view:"toolbar", elements:[
				{ view:"label", label:"Demo" },
				{ view:"segmented", localId:"control", options:[
					"details",
					"dash"
				]}
		]};
	}
	init(ui, url){
		this.use(JetApp.plugins.Menu, {
			id:"control",
			urls:{
				details  : "demo/details",
				dash : "demo/dash"
			}
		});
	}
}
~~~

[Check out the demo >>](https://github.com/webix-hub/jet-start/blob/master/sources/views/top.js)

### [<span id="unload">UnloadGuard Plugin &uarr;</span>](#contents)

The **UnloadGuard** plugin can be used to prevent users from leaving the view on some conditions. For example, this can be useful in the case of forms with unsaved or invalid data. The plugin can intercept the event of leaving the current view and, e.g. show the confirmation dialogue.

![](../images/unload.png)

The plugin reacts to an attempt of changing the URL. The syntax for using a plugin is *this.use\(plugin,handler\)*.

```js
// views/some.js
...
init(ui, url){
	this.use(JetApp.plugins.UnloadGuard, handler_function);
}
```

*this.use* takes two parameters:

- the plugin name
- the function that will define the behavior of the plugin

The *UnloadGuard* plugin can be used for form validation, for example. Let's have a look at a form with one input field that mustn't be empty:

```js
// views/form.js
import {JetView} from "webix-jet";

export default class FormView extends JetView {
    config(){
        return { 
            view:"form", elements:[
                { view:"text", name:"email", required:true, label:"Email" },
                { view:"button", value:"save", click:() => this.show("Details") }
            ]
        };
    }
}
```

Let's switch on the *UnloadGuard* plugin and use it for form validation:

```js
// views/form.js
...
init(ui, url){
	this.use(JetApp.plugins.UnloadGuard, () => {
		if (this.getRoot().validate())
			return true;

		return new Promise((res, rej) => {
			webix.confirm({
				text: "Are you sure?",
				callback: a => a ? res() : rej()
			})
		});
	});
}
```

If the form input isn't valid, the function returns a promise with a dialogue window. Depending on the answer, the promise either resolves and the *Guard* lets the user go to the next view, or it rejects. No pasaran.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/plugins-unload.js)

### [<span id="status">Status Plugin &uarr;</span>](#contents)

This plugin is useful if you want to show the status of data loading in case it takes time, to confirm success or to show an error message. 

![webix jet plugin status demo](../images/plugin_status.png)

These are the status messages that you can see:

- "Ok"
- "Error"
- "Connecting..."

This is how you can use the plugin to display the status of data loading into a datatable:

```js
// views/data.js
import {data} from "../models/records";
...
config(){
	return {
		rows:[
			{ view:"datatable", autoConfig:true },
			{ id:"app:status", view:"label" }
		]
	};
}
init(view){
	view.parse(data);

	this.use(plugins.Status, { 
		target: "app:status",
		ajax:true,
		expire: 5000
	});
}
```

The **target** property is the ID of the view component where you want to display the message (a label in this example).

*ajax:true* enables asynchronous requests.

*expire* defines the time after which the status message disappears (5 seconds in this case). By default, the time is set to 3 seconds, and if you set it to 0, the status message will stay as long as the view is open.

**Status** can have two more properties in its config:

- **data** - a property that defines the ID of the data component to track.
- **remote** - a Boolean property that enables [*webix.remote*](https://docs.webix.com/desktop__webix_remote.html) - a protocol that allows the client component to call functions on the server directly.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/plugins-status.js)

### [<span id="urlparam">UrlParam Plugin &uarr;</span>](#contents)

The plugin allows using the URL fragments as parameters. It makes them accessible via **getParam(name)** and correctly weeds them out of the URL.

Let's consider a simple example with a parent view **some** and its child **details**:

```js
// views/some.js
import {JetView} from "webix-jet";

export default class SomeView() extends JetView{
	config(){
		return {
			rows:[
				{ $subview:true }
			]
		};
	}
}

// views/details.js
const details = { template:"Details" };
export default details;
```

When loading the URL *"/some/23/details"*, you need to drop *23* and treat it as a parameter of **some**. This will be done by the plugin. Launch it in the **init** method of **some**:

```js
// views/some.js
import {JetView,plugins} from "webix-jet";

export default class SomeView() extends JetView{
   ...
   init(){
       this.use(plugins.UrlParam, ["id"])
       // now when loading /some/23/details
       var id = this.getParam("id");//id === 23
   }
}
```

In this example, **details** will be rendered inside **some**, and the fragment *23* will be dropped.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/urlparams.js)

### [<span id="user">User Plugin &uarr;</span>](#contents)

The *User* plugin is useful for apps with authorization. The plugin launches the **user** service.

![](../images/plugin_user.png)

#### Login through a Custom Script

##### The Session Model

The plugin uses a **session** model, [check it out](https://github.com/webix-hub/jet-start/blob/php/sources/models/session.js). It contains requests to *php* scripts for logging in, getting the current status, and logging out. The *session* model includes the following functions:

- **status** - returns the status of the current user:

```js
// models/session.js
function status(){
	return webix.ajax().post("server/login.php?status")
		.then(a => a.json());
}
```

- **login** - logs the user in, returns an object with his/her access right settings, a promise of this object or *null* if something went wrong. The parameters are:

- *user* - username;
- *pass* - password.

```js
// models/session.js
function login(user, pass){
	return webix.ajax().post("server/login.php", {
		user, pass
	}).then(a => a.json());
}
```

- **logout** - logs the user out:

```js
// models/session.js
function logout(){
	return webix.ajax().post("server/login.php?logout")
		.then(a => a.json());
}
```

##### Plugin Usage

To use a plugin, call *app.use* and pass the **session** model to it:

```js
// myapp.js
import session from "models/session";
...
app.use(plugins.User, { model: session });
```

This is an example of a form for logging in:

```js
// views/login.js
import {JetView} from "webix-jet";

export default class LoginView extends JetView{
config(){
	return {
		view:"form",
		rows:[
			{ view:"text", name:"login", label:"User Name", labelPosition:"top" },
			{ view:"text", type:"password", name:"pass", label:"Password", labelPosition:"top" },
			{ view:"button", value:"Login", click:function(){
				this.$scope.do_login(); //do_login is implemented as a class method below
			}, hotkey:"enter" }
		],
		rules:{
			login:webix.rules.isNotEmpty,
			pass:webix.rules.isNotEmpty
		}
	};
} 
```

The form has two validation rules for the text fields.

##### Logging In

To implement the way to log in, you can use the **login** method of the *user* service that is launched by the *User* plugin.

**login(name, password)**

**login** receives the username and the password, verifies them and if everything's fine, shows the *afterLogin* page. Otherwise, it shows an error message. 

Here's how the **do_login** method of the Jet class view is implemented:

```js
// views/login.js
import {JetView} from "webix-jet";

export default class LoginView extends JetView{
	...
	do_login(){
		const user = this.app.getService("user");
		const form = this.getRoot();

		if (form.validate()){
			const data = form.getValues();
			user.login(data.login, data.pass).catch(function(){
				//error handler
			});
		}
	}
	...
}
```

If users submit their name and password, *user.login* is called. You can add an error handler for an invalid username and password.

**Related demo**:

- The [complete *login.js* file](https://github.com/webix-hub/jet-start/blob/php/sources/views/login.js);
- The [demo on logging in with custom scripts](https://github.com/webix-hub/jet-start/tree/php).

The **User** plugin has other useful methods.

##### Getting User Info

**getUser()**

**getUser** returns the data of the currently logged in user.

**getStatus()**

**getStatus** returns the current status of the user. It can receive an optional Boolean parameter *server*. The **User** service checks every 5 minutes the current user status and warns a user if the status has been changed. For example, if a user logged in and didn't perform any actions on the page during some time, the service will check the status and warn the user if it has been changed.

##### Logging Out

**logout()**

**logout** ends the current session and shows an afterLogout page, usually it's the login form.


#### Login with an external OAuth service ( Google, GitHub, etc. )

tbc <!-- to be continued -->


### [<span id="theme">Theme plugin &uarr;</span>](#contents)

This is a plugin to change app themes. This is how you can switch the plugin on:

```js
// myapp.js
import {JetApp, plugins} from "webix-jet";
...
app.use(plugins.Theme);
app.render();
```

The plugin launches the **theme** service. There are two methods that the service provides:

###### getTheme()

The method returns the name of the current theme.

###### setTheme(name)

The method takes one obligatory parameter - the name of the theme - and sets the theme for the app.

#### Adding Stylesheets

The service locates links to stylesheets by their *title* attributes. Here are the stylesheets for the app, e.g.:

```html
// index.html
<link rel="stylesheet" title="flat" type="text/css" href="//cdn.webix.com/edge/webix.css">
<link rel="stylesheet" title="compact" type="text/css" href="//cdn.webix.com/edge/skins/compact.css">
```

#### Setting Themes

Next, you need to provide a way for users to toggle themes. Here's a view with a segmented button:

```js
// views/settings.js
import {JetView} from "webix-jet";

export default class SettingsView extends JetView {
	config(){
		return {
			type:"space", rows:[
				{ template:"Settings", type:"header" },
				{ name:"skin", optionWidth: 120, view:"segmented", label:"Theme",
					options:[
						{ id:"flat-default", value:"Default" },
						{ id:"flat-shady", value:"Shady" },
						{ id:"compact-default", value:"Compact" }
					], click:() => this.toggleTheme() /* not implemented yet, will be a method of this class */
				}
				{}
			]
		};
	}
}
```

Note that option IDs of the segmented should have two parts. The first part must be the same as the *title* attribute of the link to a stylesheet. The **theme** service must get the theme name from this part of the ID. After that, the service locates the correct stylesheet and sets the theme.

Let's implement toggling themes as a method of the *SettingsView* class:

```js
// views/settings.js
import {JetView} from "webix-jet";

export default class SettingsView extends JetView {
	...
	toggleTheme(){
		const themes = this.app.getService("theme");
		const value = this.getRoot().queryView({ name:"skin" }).getValue();
		themes.setTheme(value);
	}
}
```

Inside the method, the **queryView** locates the segmented by its name and gets the user's choice. After that, the service sets the chosen theme by adding a corresponding CSS class name to the body of the HTML page.

#### Restoring The State of the Control

To restore the state of the segmented button after the new theme is applied, you need to get the current theme in the class **config** and add the **value** property of the segmented setting it to the correct value:

```js
// views/settings.js
...
config(){
	const theme = this.app.getService("theme").getTheme();
    return {
		...
		{ name:"skin", optionWidth: 120, view:"segmented", label:"Theme", options:[
			{id:"flat-default", value:"Default"},
			{id:"flat-shady", value:"Shady"},
			{id:"compact-default", value:"Compact"}
		], click:() => this.toggleTheme(), value:theme }
		...
	};
}
```

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/plugins-theme.js)

### [<span id="locale">Locale plugin &uarr;</span>](#contents)

This is a plugin for localizing apps.

![](../images/plugin_locale.png)

Locale files are usually created in the *locales* folder. This is an example of the Spanish locale file:

```js
// locales/es.js
export default {
	"Settings" : "Ajustes",
	"Language" : "Idioma",
	"Theme" : "Tema"
};
```

This is how you can switch the Locale plugin on:

```js
// myapp.js
import {JetApp, plugins} from "webix-jet";
...
app.use(plugins.Locale);
app.render();
```

#### Optional Path for the Locale Plugin

You can create subfolders inside the *jet-locale* folder and set the path while launching the plugin:

```js
// app.js
...
app.use(plugins.Locale, { path:"some"});
```

where *path* is a subfolder (subpath) inside the *jet-locale* folder.

#### Locale Service Methods

The Locale plugin launches a service. The **locale** service has several methods for localizing apps.

**the _(value) helper**

This helper looks for the field in a locale file and returns the translated value. E.g. *this.app.getService("locale")._("Settings")* will return *"Ajustes"*, if Spanish is chosen. If you need to localize a lot of text labels, it's reasonable to create a shorthand for the method:

```js
const _ = this.app.getService("locale")._;
```

**getLang()**

The method returns the current language.

**setLang(name)**

The method sets the language passed by the name of the locale file. It also calls **app.refresh** and re-renders all the views.

#### Using Locale Plugin

When a user chooses a language, a locale file is located and the app language is changed. 

Let's create a segmented button that will be used to toggle languages:

```js
// views/settings.js
import {JetView} from "webix-jet";

export default class SettingsView extends JetView {
	config(){
		return {
			type:"space", rows:[
				{ template:"Settings", type:"header" },
				{ name:"lang", optionWidth: 120, view:"segmented", label:"Language", options:[
					{ id:"en", value:"English" },
					{ id:"es", value:"Spanish" }
				], click:() => this.toggleLanguage() }, //will be implemented as a method of this class
				{}
			]
		};
	}
}
```

Note that IDs of the language options should be the same as the locale file names (e.g. "es", "en").

#### Setting the Language

To set the locale, you need to get the value of the segmented button and pass it to **setLang** that will set the locale. This can be done in the **toggleLanguage** method of the *SettingsView* class:

```js
import {JetView} from "webix-jet";

export default class SettingsView extends JetView {
	config(){
		return {
			...
			{ name:"lang", view:"segmented", label:"Language",
				options:[
					{id:"en", value:"English"},
					{id:"es", value:"Spanish"}
				], click:() => this.toggleLanguage()
			}
			...
		};
	}
	toggleLanguage(){
		const langs = this.app.getService("locale");
		const value = this.getRoot().queryView({ name:"lang" }).getValue();
		langs.setLang(value);
	}
}
```

To apply the new locale, use the **_** helper. Apply the helper to all the labels you want to translate:

```js
// views/settings.js
import {JetView} from "webix-jet";

export default class SettingsView extends JetView {
	config(){
		const _ = this.app.getService("locale")._; 				//shorthand

		return {
			type:"space", rows:[
				{ template:_("Settings"), type:"header" },
				{ name:"lang", optionWidth: 120, view:"segmented", label:_("Language"), options:[
					{ id:"en", value:"English" },
					{ id:"es", value:"Español" }
				], click:() => this.toggleLanguage() },
				{}
			]
		};
	}
	...
}
```

So, if the Spanish locale is set, the **_** helper will search for the translation in the locale file and return it.

#### Restoring the State of the Control

**getLang** in *config* returns the current locale, which is used to restore the value of the segmented button.

```js
import {JetView} from "webix-jet";

export default class SettingsView extends JetView {
	config(){
		const lang = this.app.getService("locale").getLang();

		return {
			...
			{ name:"lang", view:"segmented", label:_("Language"), options:[
				{id:"en", value:"English"},
				{id:"es", value:"Spanish"}
			], click:() => this.toggleLanguage(), value:lang }
			...
		};
	}
	...
}
```

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/plugins-locale.js)


## 2.Custom Plugins

You can define your own plugins.