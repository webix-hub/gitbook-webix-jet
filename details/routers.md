# Routing

To manipulate the URL, views have Routers. Webix Jet has four predefined types of routers.

## 1. Hash Router \(default\)

The app URL is displayed after a hashbang. As this router is set by default, there's no need to to define it in the config.

```js
// myapp.js
var app = new JetApp({
    start: "/demo/details",
    router: JetApp.routers.HashRouter //optional
}).render();
```

## 2. URL Router

The app URL is displayed without a hashbang. Clandestine and cool, but there's a trick with this router. Your server-side code should be compatible.

Have a look at the example. Here are three views: one parent and two child views that are dynamically included by a click on jet links in the parent view:

```js
// views/top.js
const TopView = {
	type:"space", rows:[
		{ type:"header", template:"Url router"},
		{
			type:"wide", cols:[
				{ width:200, css:"navblock", template:`
					<a route="/top/start"> - show start</a>
					<a route="/top/details"> - show details</a>
				`},
				{ $subview: true }
			]
		}
	]
};

const StartView = {
	template:"Start page"
};

const DetailsView = {
	template:"Details page"
};
```

Let's create an app from these views and choose UrlRouter:

```js
// myapp.js
import {JetApp, UrlRouter} from "webix-jet";

webix.ready(() => {
	const app = new JetApp({
		id:			"routers-url",
		router:		UrlRouter,
		routerPrefix: "/routers-url", //!
		start:		"/top/start"
	});
	app.render();
});
```

Note that there is a router prefix that is present in the URL instead of a hashbang. You must provide it if the app is hosted in a folder. In your *index.html* you should set the relative URL with the same prefix:

```html
 <!-- index.html -->
<script type="text/javascript">
	if(document.location.pathname == "/index.html")
		document.location.href = "/routers-url/";
</script>
```

Next, configure http redirects so that requests to all URLs triggered the html file of the app. This can be done through the webpack config:

```js
// webpack.config.js
devServer:{
	historyApiFallback:{
		index : "routers-url.html"
	}
}
```
In the production app, it can be done through *apache/nginx* configuration.

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/routers-url.js)

## 3. Store Router

With this guy, the app URL isn't displayed at all, but it is stored in the session storage. So no worries, you can still return to the previous and next views as if they are in the URL. This can be useful if you have a multilevel application \(apps are subviews of other apps\). The Store router is set for the enclosed apps because there's only one address bar and it's already taken by the outer app. Suppose you have closed an app module with a deep level of subviews and expect to be in the same place of this app when you switch to it again. The Store router allows this.

Here's an app module with a form view:

```js
// app1.js
var app1 = new JetApp({
    start: "/form",
    router: JetApp.routers.StoreRouter
});
```

Next, the app module is included into a view and the view is included into another app:

```js
// app2.js
const PageView = () => ({
    rows: [app1]
});

var app2 = new JetApp({
    start: "/page",
    router: JetApp.routers.HashRouter
}).render();
```

## 4. Empty Router

If you don't want to store the app part of the URL, there's the EmptyRouter for you. The app URL isn't displayed and isn't stored. It's used for nested apps because there is only one address bar. 

```js
// app1.js
var app1 = new JetApp({
    start: "/form",
    router: JetApp.routers.EmptyRouter
});
```

[Check out the demo >>](https://github.com/webix-hub/jet-demos/blob/b686944b383745070fc977aa9123f01a36ce2b3c/sources/viewapp.js)

## 5. Custom Routers

If these four routers aren't what you want, you can define your own.

