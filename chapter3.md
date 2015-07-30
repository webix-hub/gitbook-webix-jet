## Creating popups with $windows


In the previous part we’ve described a popup creation by means of the scope. It's also possible to initialize a popup with the help of the *$windows* parameter. It allows creating several views at once when the current view is shown and destruct them when the current view is destroyed.

In the example below the code creates a list view and a few popups.

```js
define([
	"models/records"
],function(records){
	return {
		$ui: {
			view:”list”, click:function(){ $$("p1").show();}
		},
		$windows:[
			{ view:"popup", id:"p1" }, 
			{ view:"popup", id:"p2" }, 
			{ view:"popup", id:"p3" }
		],
		$oninit:function(view,scope){
			view.parse(records.data);
		},
	}
});
```

When a new view will be loaded on the page, all the popups will be destroyed together with the list view.
		

##Creating connections between views

Since our app is split into several views, each of them is located in a separate file and "doesn't know" about all the others. On the one hand, it's made for the safety's sake: we can't break the whole app by spoiling the code of one view. On the other hand, views should be connected in some way in order to communicate. There are several methods to do it. 

###Triggering events

Communication between views can be implemented through the global event bus. You can attach an event handler to the global event bus in one view and trigger the event in another view.  For example, in one view we will have the following code:

```js
define([
	"app",
	"models/records"
],function(app, records){
	return {
		$ui: { view:"datatable" },
		$oninit:function(){
			app.attachEvent("detailsModeChanged", function(data){
				if (data == "all")
					this.showColumn("col1")
			})
		}
	}
});
```
The view has an event handler "detailsModeChanged" which shows a particular column when the event is triggered. 

In another view a segmented button is initialized. A click on the button triggers the call of the "detailsModeChanged" event defined in the above view:

```js
define([
	"app",
	"models/records"
],function(app, records){
	return {
		view:"segmented", click:function(){
			app.callEvent("detailsModeChanged", [ this.getValue() ])
		}
	}
});
```
Thus, on clicking the segmented button the detailsModeChanged event will fire and the "col1" column will be rendered in the datatable.


#### Event handler shortcuts ( or aliases )

Handlers of the events which are used for connecting views can be presented in a more compact way.

For example, *app.attachEvent* 

```js
app.attachEvent("eventName", function(){})
```
can be replaced with *app.on*
```js
app.on ("eventName", function(){})
```
Instead of the *callEvent* method 

```js
app.callEvent("eventName", [ ... ])
```
we can use the *app.trigger* one 

```js
app.trigger("eventName", [ ... ])
```
<br>

There's also the *app.action()* method that unites ****both the *click* *handler and the* *callEvent* method.**** 
For example, let's consider the code of initialization of a segmented button:

```js
...
	return {
		view:"segmented", on:{
			onItemClick:function(id){
				app.callEvent("detailsModeChanged", [ id ])
			}
		}
	}
```
We can simplify it in the following way:

```js
	return {
		view:"segmented", on:{
			onItemClick:app.action("detailsModeChanged")
		}
	}
```

It's also possible to replace the `$oninit` property and the attachEvent method with the `$on` property. Thus, the above code of datatable initialization 

```js
//top/grid
...
	return {
		$ui: { view:"datatable" },
		$oninit:function(){
			app.attachEvent("detailsModeChanged", function(data){
				if (data == "all")
					this.showColumn("dummy")
			})
		}
	}
```
can be shortened as in

```js
...
return {
	$ui: { view:"datatable" },
	$on:{
		detailsModeChanged: function(data){
			if (data == "all")
				this.showColumn("dummy")
		}
	}
}
```

###Declaring and calling methods

One more effective way of views connecting is the usage of methods. In one of the views we define a handler that will call some function and in another view we call this handler. 

Unlike events, methods not only call actions in views, but also can return something useful. However, this variant can only be used when we know that a view where a necessary method is declared exists. It's better to use this variant when there is a parent view and a child one. A method is declared in the child view and is called in the parent one.

Let's have a look at the example below:

```
// views/list
{
	$ui:{ view:"list", id:"mylist"},
	truncateAll:function(){
		$$("mylist").clearAll();
	},
	getActiveRecord:function(){
		return list.getSelectedId();
	}
}
```

The code of a view creates a list component. It this view we declare the *truncateAll()* method which defines a function for clearing the list's content. Additionally, the *getActiveRecord* handler specifies a function that returns a record selected in the list.

In another view we have the following code:

```
define(["list"], function(list){
	var button = {
		click:function(){
			alert(list.getActiveRecord());
			list.truncateAll();
		}
	}
	return {
		$ui:{ rows:[ list, button ] },  
	}
})
```
Here we specify a button and place our list and this button in two rows. By clicking the button we get the id of the active record and call the *truncateAll()* method which clears the list in the above view.


###Using a shared state 

There's one more way to organize views communication. It's possible to create a separate module that will keep a state which is common for other views. For example, when we select some record in one view, its id will be kept in the state module and another view will take this id. Thus, if we rerender the second view after some time, it will take the current id from the state module and display correct data.

Let's consider a more concrete case. Imagine that you have two views: one with a grid that contains names of users and one more view somewhere in the app that has a button. To share data between the grid and the button, we will define a separate model file.

```
// models/state.js
return {
	// state config
};
```

```
//views/data.js
define([
	"models/state"
], function(state){
	return {
		view:"datatable", on:{
			onItemClick: function(id){
				state.selectedId = id;
			}
		}
	}
})
```
```
//views/top.js
define([
	"models/state"
], function(state){
	var button = {
		click:function(){
			this.$scope.show("./details/"+state.selectedId);
		}
	}
	return {
		$ui:{ rows:[ grid, tabbar, { $subview:true  }] }, 
	}
})
```

When we select a row in the grid with the name of some user, the code stores the selected row's id in the model. When we need to access this info in the click handler of the subview with the button, we can retrieve it from the same model object. For example, in the above snippet, we will load subview with data for the selected record. 

###Which way to choose?

Let's summarize when it's better to use this or that way of creating connections between views.

Events are helpful when one view doesn't know that another view exists, but it should react to the action that takes place in this view.

Methods are handy when there is a view enclosed into another one. This way allows you to do some actions in the child view and return some data.

The usage of a module with a shared state allows keeping some state common for several views in a separate file. This way is rather useful, as different views can have access to common data. You should be aware which views use the same state, as changes made in the state module will affect them all.


##Asynchronous UI loading

Sometimes we can include data used in a view directly in the view description. In such a case we deal with the so-called "hardcoded" values. For example, we have a chart and need to define the colors of its lines, specified in the *series* parameter:

```js
var ui = {
	view:"chart",
	series:[{ value:"sale", color:"red"},{value:"expenses", color:"green"}]
};
```

However, in practice some configuration settings in our UI can be stored in the database. For example in the above snippet we may want to store colors in DB to allow their customization by end user. (Replace hardcoded values with DB based ones)

In such case, a module can return a promise of UI instead of UI configuration. 

Let's have a look at the code below:

```js
define([
	"models/records"
],function(records){
	return webix.ajax("colors.php").then(function(data){
		data = data.json();
		
		var ui = {
			view:"chart",
			series:[
				{ value:"sale", color:data[0].color},
				{ value:"expenses", color:data[1].color}
			]
		};
		
		return ui;
	});
})
```
In the above code all the colors used for lines of the chart are stored in DB and are returned by "colors.php" script. *webix.ajax* call  sends an asynchronous request to the "colors.php" script on the server and returns promise of data instead of real data. All data should come to the client first and only after that final view configuration constructed and view will be rendered. 

There are several ways to implement asynchronous data loading:

- [webix.ajax](http://docs.webix.com/helpers__ajax_operations.html) that makes an asynchronous request to a PHP script and shows it response through a callback function;
- [data.waitData](http://docs.webix.com/api__ui.dataview_waitdata_other.html) which is used for data components, such as datacollection, list, tree, datatable, etc;
- [webix.promise](http://docs.webix.com/helpers__ajax_operations.html#promiseapiforajaxrequests) that allows treating the result of asynchronous operations without callbacks.


## Multi-language support

You can easily localize your application by using a special plugin. All you need to enable localization is to include the *locale.js* file into the app.js and add the *app.use(locale)* line in the configuration:

```
// app.js
define([
	"webix-mvc-core/plugins/locale"
]),function(locale){
	// configuration
	...
	app.use(locale);
}) 
```
In the view which needs to be localized the "locale" dependency should be included. This dependency will provide the translating function. All the text values that need to be translated must be wrapped into this function:

```
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

```
// locales/en.js
define ([], function(){     
	return {  "Language": "Language" };
});

// locales/fr.js
define([],function(){     
	return { "Language": "Langue"}; 
);
```

As you can see, this is a collection of *key:value* pairs, where the key is the name of the text string used in a view file, and the value is the translation. 

###Setting the language 

By default, English locale is set. You can specify the default language in the *app.js* file.

```
app.use(locale, { lang:"ru" })
```
To change the active language of the app, use the *setLang()* method. It takes a two-letter code of the language as the argument:
```
locale.setLang("ru");
```
It's also possible to return the currently set language, using the *getLang()* method:

```
locale.getLang();
```

###Benefits of localization

The basis of Webix MVC localization is [Polyglot.js](http://airbnb.io/polyglot.js/) library. Besides usual words substitution, it can insert necessary parameters into a line and create phrases in the right form depending on the number of a noun.

To get a pluralized phrase,  a special string is used. The plural forms are separated by the delimiter "||||", or four vertical line characters.

```
define(["locale"], function(_){
	  var count = 100;
	  return {
	    $ui:{ view:"label", label:_("BooksCountLabel", count) }
	  }
});
```

```
//locale/en.js
define(function(){     
	return {         
		BooksCountLabel:"You have %{count} book |||| You have %{count} books"
	}
});
```

To understand the difference better, check the next example:
```
var label = _("BooksCountLabel",{count:1}); // You have 1 book
var label = _("BooksCountLabel",{count:50}); // You have 50 books
```

You can find more information in the [documentation of Polyglot.js](http://airbnb.io/polyglot.js/).

##The structure of an URL and folders

A typical URL looks as http://some.com/index.html#!/top/child/subchild. The part that goes after **#!** is the  current state of the app. Each segment is the name of a file from the *views* directory.

The order of views rendering in the above url is the following:

 - views/top.js is rendered
 - if there is some space for a child element (subview) in top.js, views/child.js is taken and rendered in the defined place 
 - if child.js has some space for its own child element, views/subchild.js is rendered in the defined place

If you need to include a file from a subdirectory, e.g. details/subchild.js instead of a subchild, the dot notation is used: http://some.com/index.html#!/top/child/details.subchild. Let's consider more examples:

The */top/child* directory implies that two views will be loaded:

 - views/top.js
 - views/child.js

The path */top/folder.child* has the following views hierarchy:

 - views/top.js
 - views/folder/child.js

A more complex structure can look as */top/folder.child/folder.subfolder.grand.js*. It includes the following directories and subdirectories:

- views/top.js
- views/folder/child.js
- views/folder/subfolder/grand.js

##Using unique IDs

Webix components can have ids. They are used to refer to this or that component, which is rather handy. The important moment is that ids must be unique ( that is requirement of Webix UI, as code can't distinguish between two views with the same id ). There are three ways to make a unique id:

- create a complex id that have the structure *{viewname}:{role}*, for example: *"start:view"*. By using the view's name in the 1st part of id we make it unique;
- generating a unique id:

```
var listid = webix.uid();
return {
    $ui:{ id:"r1", rows:[
	    { view:"list", id:listid }
	]},
    clear:function(){ $$(listid).clear(); }
}
```
Here we have a layout that contains a list and call a function that clears the list by its id. Each time the code creates a layout, it generates a new id for it.

While you can get a view by its id from any module of your app, it is strongly recommended to call an element by id only inside of the same module where this element was defined. 

- isolated ID spaces

```
return {
    $ui:{ isolate:true, rows:[
		{ view:"list", id:"mylist" },
		{ view:"button", value:"clear", click:function(){
			var layout = this.getTopParentView();
			layout.$$("mylist").clear();
		})
	]}
}
```
The above code contains an isolated layout that includes a list and a button. By clicking the button, the layout is cleared. As our layout is located in an isolated space, we need to call the *getTopParentView()* method to refer to the isolated id "*mylist"* to call the *clear* method, as there are can be the same global ids. 

###Creating ids for multiple displaying of a view
Let's consider the case when it's necessary to show the same view on the screen several times. 

We can't directly specify the id of the view as it's made in the first variant, because each separate instance of the view must have a unique id. The second variant implies that a unique id will be generated just once, which is also improper for multiple displaying of a view.

As for the third variant, it will do fine for our needs. By using isolated ID spaces you can repeatedly use the same view on the page, as isolated ids will be treated as unique.

There is one more way. We can specify a function that will return a view with a new id each time the code is executed:

```js
define([],function(){
	return {
	    $ui:function(){
	    	var listid = webix.uid();
		    return { rows:[
			    { view:"list", id:listid },
   			    { view:"button", value:"clear", click:function(){ $$(listid).clearAll(); }}
  			]};
		}
	}
})
```
Thus, the above code will create each new instance of a list view with a unique id.