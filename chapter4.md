## Building and deploying an application

You need to have Node.js installed to use the build system. To start the building process, run the following command from the project’s directory:

> npm install

If you want to check all js code, run the next command:

> gulp lint

To build the final app to deploy on the server, use

> gulp build

The deployed application consists of the following parts:

- app.js -  includes all the views and models files; the file is as small as 10kb
- app.css - contains all the styles applied in the app
- index.html - the index page of an app
- assets/ - the used styles and images

**What to do if you've modified the code, but nothing changes on the screen**

When you refresh a page in the browser, the changes you’ve made in the app configuration may not display. It happens because a browser tries to cache the repeatedly used files in case there are a lot of them in the code.
To solve this issue, we recommend to disable caching in the Developer tools of your browser during the development process.


## Complex navigation

Besides simple urls used for navigation between views, we can create more complex urls that will allow restoring the app’s state after the page's reloading. 

Each segment of an url can contain certain parameters. These parameters can keep the id of the selected record, the mode of a segmented button that determines the state of the application, etc.

For example, if we select a row with the id "1" in the grid in the top view and check some checkbox in the child view, the url will look as follows: 

*“index.html#!/top?id=1/child?checked=true”*

In general, if it’s needed to render a view with selected data and show a subview inside of it, we can use the this.$scope.show() method and pass the id of the record that should be selected as an argument:

```js
// /data
this.$scope.show({ id:1 });
```
In the url of the page the id of the selected record follows the view name and then the subview name goes:

*“index.html#!/data?id=1/form"*

It's possible to pass several parameters of selected records:

```js
// /data
this.$scope.show({ id:1, task:2});
```

In this case the above url will have the following format:

*“index.html#!/data?id=1&task=2/form"*

`this.$scope.show()` allows  reloading just the part of the app. If the app should be changed starting from the top view, *this.$scope.show()* and *app.show()* methods can be used with no difference:

```js
// "data" is the top view, the result of both methods' work will be the same
this.$scope.show("/data?id=3/form") 
app.show("/data?id=3/form")
```
To restore the state of the app after the page’s reloading, the `$onurlchange` handler is used. 

For example, we have a layout with two lists and we want to reload these lists with selected records kept. So, we'll specify the '$onurlchange' handler after the layout definition and pass the views configuration to it:

```js
define([
    "models/records"
],function(records){
	return:{
		$ui:{
			{ rows:[
				{view:"list", id:"mylist1", select:true, click:function(id){
					this.$scope.show({ id: id });
	 		 	 },
	   			{view:"list", id:"mylist2", select:true, click:function(id){
					this.$scope.show({ task: id });
	   			}
	   		]
		},
		$onurlchange:function(view, config, url, scope){
			if (config.id)
				$$("mylist1").select(id);
			if (config.task)
				$$("mylist2").select(task);
		}
	}
});
```
The handler will be called each time the url changes and restore the state of the views after reloading the application.