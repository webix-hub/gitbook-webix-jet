## Creating popups with $windows


In the previous part we’ve described a popup creation by means of the scope. It's also possible to initialize a popup or a window with the help of the *$windows* parameter. It allows creating several views at once when the current view is shown and destruct them when the current view is destroyed.

In the example below the code creates a list view and a few popups.

```js
define([
	"models/records"
],function(records){
	return {
		$ui: {
			view:"datatable", on:{
			    onItemClick:function(){
				    $$("win2").show();
			    }
		    }
		},
		$windows:[
			{ view:"window", id:"win1" }, 
			{ view:"popup", id:"win2" }
		],
		$oninit:function(view,$scope){
			view.parse(records.data);
			$$("win1").show();
		},
	}
});
```

When a new view will be loaded on the page, all the popups and windows will be destroyed together with the list view.
		

##Creating connections between views

Since our app is split into several views, each of them is located in a separate file and "doesn't know" about all the others. On the one hand, it's made for the safety's sake: we can't break the whole app by spoiling the code of one view. On the other hand, views should be connected in some way in order to communicate. There are several methods to do it. 

###Triggering events

Communication between views can be implemented through the global event bus. You can attach an event handler to the global event bus in one view and trigger the event in another view.  For example, in one view we will have the following code:

```js
//views/data.jd

define([
	"app",
	"models/records"
],function(app, records){
	var ui = {
		view:"datatable",
		visibleBatch:"info",
		columns:[
			{ id:"title", header:"Title", fillspace:true },
			{ id:"year", header:"Year", batch:"info" },
			{ id:"votes", header:"Votes", batch:"stats" },
			{ id:"rating", header:"Rating", batch:"stats", hidden:true },
			{ id:"rank", header:"Rank", batch:"stats", hidden:true }
		]
	};

	return {
		$ui: ui,
		$oninit:function(view){
			view.parse(records.data);

			app.attachEvent("detailsModeChanged", function(mode){
                view.showColumnBatch(mode);
            });
		}
	};
});
```
The view has an event handler "detailsModeChanged" which shows a particular column group  when the event is triggered. 

In another view a segmented button is initialized. A click on the segmented button triggers the call of the "detailsModeChanged" event defined in the above view:

```js
// views/top.js

define([
	"app"
],function(app){
	
	var mode =  { 
	    view:"segmented", options:[
			{id:"info", value:"Info"},
			{id:"stats", value:"Stats"}
		],
		on:{
			onChange:function(newv){
				app.callEvent("detailsModeChanged", [newv]);
			}
		}
	}
	...
});
```
Thus, on clicking the segmented button the detailsModeChanged event will fire and the corresponding  column group will be rendered in the datatable.


#### Event handler shortcuts ( or aliases )

Handlers of the events which are used for connecting views can be presented in a more compact way.

For example, *app.attachEvent* 

```js
app.attachEvent("eventName", function(){});
```
can be replaced with *app.on*
```js
app.on ("eventName", function(){});
```
Instead of the *callEvent* method 

```js
app.callEvent("eventName", [ ... ]);
```
we can use the *app.trigger* one 

```js
app.trigger("eventName", [ ... ]);
```
<br>

There's also the *app.action()* method that unites both the *click* *handler and the* *callEvent* method. For example, let's consider the code of initialization of a segmented button:

```js
// views/top.js

	var mode = { 
	    view:"segmented", options:[
			{id:"info", value:"Info"},
			{id:"stats", value:"Stats"}
		],
		on:{
			onChange:function(newv){
				app.callEvent("detailsModeChanged", [newv]);
			}
		}
	};
```
We can simplify it in the following way:

```js
	var mode =  {
		view:"segmented", options:[
			{id:"info", value:"Info"},
			{id:"stats", value:"Stats"}
		], 
		on:{
			onChange:app.action("detailsModeChanged");
		}
	}
```

It's also possible to replace the `$oninit` property and the attachEvent method with the `$on` property. Thus, the above code of datatable initialization 

```js
//views/data.js

...
	return {
		$ui: { view:"datatable" },
		$oninit:function(view){
			app.attachEvent("detailsModeChanged", function(mode){
				view.showColumnBatch(mode);
			});
		}
	}
```
can be shortened as in

```js
...
return {
	$ui: { view:"datatable", id:"data:table" },
	$on:{
		detailsModeChanged: function(mode){
			$$("data:table").showColumnBatch(mode);
		}
	}
}
```

In the sample above *view* variable is not accessible, so we need to address the datatable by id.

###Declaring and calling methods

One more effective way of views connecting is the usage of methods. In one of the views we define a handler that will call some function and in another view we call this handler. 

Unlike events, methods not only call actions in views, but also can return something useful. However, this variant can only be used when we know that a view where a necessary method is declared exists. It's better to use this variant when there is a parent view and a child one. A method is declared in the child view and is called in the parent one.

Let's have a look at the example below:

```js
// views/films.js

return {
	$ui:{ view:"datatable", id:"films:table"},
	truncateAll:function(){
		$$("films:table").clearAll();
	},
	getActiveRecord:function(){
		var id = $$("films:table").getSelectedId();
		return id? $$("films:table").getItem(id): null;
	}
}
```

The code of a view creates an already mentioned datatable. It this view we declare the *truncateAll()* method which defines a function for clearing the datatable's content. Additionally, the *getActiveRecord* method specifies a function that returns a record selected in the datatable.

In another view we have the following code:

```js
// views/data.js

define([
	"views/films"
],function(films){

	var details = { view:"template", id:"data:tpl", data:{}, template:function(obj){
		return obj.id?obj.rank+obj.title;
	}};

	var ui = {
		rows:[
			{view:"toolbar", elements:[
				{ view:"button", value:"Show details", click:function(){
					var item = films.getActiveItem();
					if(item){
						$$("data:tpl").data = item;
						$$("data:tpl").refresh();
				}},
				{ view:"button", value:"Clear All", click:function(){
					films.truncateAll();
				}}
			]},
			films,
			details
		]
	};

	return {
		$ui: ui
	};
	
});
```
Here we specify a toolbar with two buttons and detailed film view and then place everything in three rows together with the datatable from the child view. 

By clicking the first button we get an object of the active datatable record and use it for *details* view while the second button calls the *truncateAll()* method which clears the datatable in the child view.


###Using a shared state 

There's one more way to organize views communication. It's possible to create a separate module that will keep a state that is common for other views. For example, when we select some item in one view, its id will be kept in the state module and another view will use this id. Thus, if we rerender the second view after some time, it will take the current id from the state module and display correct data.

Let's consider a more concrete case. In the *Triggering events* section we dealt with two views: one with a segmented button that switches data mode and one more view with a datatable to which this mode is applied. They are connected with *detailsModeChanged* event, but when you reload the view (change from *"#!/top/data"* to *"#!/top/start"* and back to top) current data mode is not applied to the datatable.

To establish a permanent connection between the button and the datatable, we will define a separate model file where dataMode will be stored.

```js
// models/state.js

define([], function(){
	return {
		dataMode:"info"
	};
});
```

```js
//views/data.js

define([
	"models/state"
], function(state){
	return {
		$ui:{ view:"datatable"},
		$oninit:function(view){
			view.parse(records.data);

			view.showColumnBatch(state.dataMode);
			app.attachEvent("detailsModeChanged", function(){
                view.showColumnBatch(state.dataMode);
            });
		}
	}
});
```
```js
//views/top.js
define([
	"models/state"
], function(state){

	var mode = { 
	    view:"segmented", value:state.dataMode,  options:[
			{id:"info", value:"Info"},
			{id:"stats", value:"Stats"}
		],
		on:{
			onChange:function(newv){
				state.dataMode = newv;
				app.callEvent("detailsModeChanged");
			}
		}
	};
});
```

When we switch a segment in the button, the code stores the selected segment's id in the model. When we need to access this info in the $oninit or "detailsModeChanged" handler of the datatable, we can retrieve it from the same model object. For example, in the above snippet, we will apply the current state to a datatable each time it is reloaded and each time another mode is selected. 

###Which way to choose?

Let's summarize when it's better to use this or that way of creating connections between views.

Events are helpful when one view doesn't know that another view exists, but it should react to the action that takes place in this view.

Methods are handy when there is a view enclosed into another one. This way allows you to do some actions in the child view and return some data.

The usage of a module with a shared state allows keeping some state common for several views in a separate file. This way is rather useful, as different views can have access to common data. You should be aware which views use the same state, as changes made in the state module will affect them all.


##Asynchronous UI loading

Sometimes we can include data used in a view directly in the view description. In such a case we deal with the so-called "hardcoded" values. For example, we have a chart on the start page and need to define the colors of its lines, specified in the *series* parameter:

```js
//views/start.js

define([
	"models/records"
],function(records){

    var ui = {
	    view:"chart",
	    series:[{ value:"#sales#", color:"#1293f8"},{value:"#sales2#", color:"#66cc00"}],
	    ...
    };
    
    return {
	    $ui:ui,
	    $oninit:function(){
	        view.parse(records.data);
	    }
	};
});	
```

However, in practice some configuration settings in our UI can be stored in the database. For example in the above snippet we may want to store colors in DB to allow their customization by end user. (Replace hardcoded values with DB based ones)

In such case, a module can return a promise of UI instead of UI configuration. 

Let's initialize such a chart in the  *view/data.js* file with the help of the code below:

```js
//view/data.js

define([
	"models/records"
],function(records){
	return webix.ajax("colors.php").then(function(data){
		data = data.json();
		//[{"id":1,"color":"#1293f8"},{"id":2,"color":"#66cc00"}]
		
		var ui = {
			view:"chart",
			series:[
				{ value:"#sales#", color:data[0].color},
				{ value:"#sales2#", color:data[1].color}
			],
			...
		};
		
		return {
		    $ui:ui,
		    $oninit:function(){
		        view.parse(records.data);
		    }
		};
	});
});
```
In the above code all the colors used for lines of the chart are stored in DB and are returned by "server/colors.php" script. *webix.ajax* call  sends an asynchronous request to the "server/colors.php" script on the server and returns promise of data instead of real data. First, all data should come to the client and only after that final view configuration will be constructed and the view will be rendered. 

There are several ways to implement asynchronous data loading:

- [webix.ajax](http://docs.webix.com/helpers__ajax_operations.html) that makes an asynchronous request to a PHP script and shows it response through a callback function;
- [data.waitData](http://docs.webix.com/api__ui.dataview_waitdata_other.html) which is used for data components, such as datacollection, list, tree, datatable, etc;
- [webix.promise](http://docs.webix.com/helpers__ajax_operations.html#promiseapiforajaxrequests) that allows treating the result of asynchronous operations without callbacks.


## Multi-language support

You can easily localize your application by using a special plugin. All you need is to include the *locale.js* file into the app.js and add the *app.use(locale)* line in the configuration:

```js
// app.js
define([
	"libs/webix-mvc-core/plugins/locale"
]),function(locale){
	// configuration
	...
	app.use(locale);
});
```
In the view which needs to be localized the "locale" dependency should be included. This dependency will provide the translating function. All the text values that need to be translated must be wrapped into this function:

```js
//views/input.js
define(["locale"], function(_){
	  return {
	    $ui:{ view:"text", value: _("Language") }
	  }
});
```
The call of the translating function will return the text in the specified locale.

###How locales are stored

Files of locales are stored as *locales/language.js*.

The format of files is the following:

```js
// locales/en.js
define ([], function(){     
	return {  "Language": "Language" };
});

// locales/de.js
define([],function(){     
	return { "Language": "Sprache"}; 
});
```

As you can see, this is a collection of *key:value* pairs, where the key is the name of the text string used in a view file, and the value is the translation. 

###Setting the language 

By default, English locale is set. You can specify the default language in the *app.js* file.

```js
app.use(locale, { lang:"de" });
```
To change the active language of the app, use the *setLang()* method. It takes a two-letter code of the language as the argument:
```js
locale.setLang("de");
```
It's also possible to return the currently set language, using the *getLang()* method:

```js
locale.getLang();
```

###Benefits of localization

The basis of Webix Jet localization is [Polyglot.js](http://airbnb.io/polyglot.js/) library. Besides usual words substitution, it can insert necessary parameters into a line as well as create phrases in the right form depending on the number of a noun.

To get a pluralized phrase,  a special string is used. The plural forms are separated by the delimiter "||||", or four vertical line characters.

```js
define(["models/records", "locale"], function(records, _){
	  var count = records.data.count();
	  return {
	    $ui:{ template:_("FilmsCountLabel", count) }
	  }
});
```

```js
//locale/en.js
define(function(){     
	return {         
		FilmsCountLabel:"You have %{smart_count} films |||| You have %{smart_count} films"
	}
});
```

To understand the difference better, check the next example:
```js
var label = _("FilmsCountLabel", 1); // You have 1 film
var label = _("FilmsCountLabel", 6); // You have 6 films
```

You can find more information in the [documentation of Polyglot.js](http://airbnb.io/polyglot.js/).

##The structure of an URL and folders

A typical URL looks as http://some.com/#!/top/child/subchild. The part that goes after **#!** is the  current state of the app. Each segment is the name of a file from the *views* directory.

The order of views rendering in the above url is the following:

 - views/top.js is rendered
 - if there is some space for a child element (subview) in top.js, views/child.js is taken and rendered in the defined place 
 - if child.js has some space for its own child element, views/subchild.js is rendered in the defined place

If you need to include a file from a subdirectory, e.g. details/subchild.js instead of a views/subchild.js, the dot notation is used: http://some.com/#!/top/child/details.subchild. 

Let's consider more examples:

The */top/start* directory implies that two views will be loaded:

 - views/top.js
 - views/start.js

The path */top/start/details.descr* has the following views hierarchy:

 - views/top.js
 - views/start.js
 - views/details/descr.js

A more complex structure can look as */top/data.films/data.about.1*. It includes the following directories and subdirectories:

- views/top.js
- views/data/films.js
- views/data/about/1.js

##Using unique IDs

Webix components can have ids. They are used to refer to this or that component, which is rather handy. The important moment is that ids must be unique ( that is requirement of Webix UI, as code can't distinguish between two views with the same id ). There are three ways to make a unique id:

- create a complex id that have the structure *{viewname}:{role}*, for example: *"start:view"*. By using the view's name (which is the name of the file where it is stored) in the 1st part of id we make it unique;
- generating a unique id with the webix.uid() method:

```js
define([
	"models/records"
],
function(records){
    var select_id = webix.uid();
    var button_id = webix.uid();
    
	var ui = {
		cols:[
			{ view:"richselect", id:select_id, options:{
				body:{	template:"#title#",	data:records.data }
			}},
			{ view:"button", id:button_id, value:"Clear", click:function(){
				$$(select_id).setValue("");
			}}
		]
	};

	return {
		$ui:ui
	};
});
```
Here we have a layout that contains a select box and a button that calls a function to clear it. Each time the code creates a layout, it generates a new id for both of them.

While you can get a view by its id from any module of your app, it is strongly recommended to call an element by id only inside of the same module where this element was defined. 

- isolated ID spaces

```js
define([
	"models/records"
],
function(records){

	var ui = {
		isolate:true, cols:[
			{ view:"richselect", id:"selectbox", options:{
				body:{	template:"#title#",	data:records.data }
			}},
			{ view:"button", id:"clear_selection", value:"Clear", click:function(){
				var selectbox = this.getTopParentView().$$("selectbox");
				selectbox.setValue("");
			}}
		]
	};

	return {
		$ui:ui
	};
});
```
The above code contains an isolated layout that includes a select box and a button. By clicking the button, the selection is cleared. As our layout is located in an isolated space, we need to call the *getTopParentView()* method to refer to the isolated id "*selectbox"* to call the *setValue* method, as there are can be the same global ids.  

###Creating ids for multiple displaying of a view
Let's consider the case when it's necessary to show the same view on the screen several times. 

We can't directly specify the id of the view as it's made in the first variant, because each separate instance of the view must have a unique id. The second variant implies that a unique id will be generated just once, which is also improper for multiple displaying of a view.

As for the third variant, it will do fine for our needs. By using isolated ID spaces you can repeatedly use the same view on the page, as isolated ids will be treated as unique.

There is one more way. We can specify a function that will return a view with a new id each time the code is executed:

```js
define([
	"models/records"
],
function(records){

	var select_id = webix.uid();
	var button_id = webix.uid();

	return {
		$ui: (function(){
			return {
				cols:[
					{ view:"richselect", id:select_id, 	options:{
						body:{	template:"#title#",	data:records.data }
					}},
					{ view:"button", id:button_id, value:"Clear", click:function(){
						$$(select_id).setValue("");
					}}
				]
			};
		})()
	};
});
```
Thus, the above code will create each new instance of a richselect view with a unique id.