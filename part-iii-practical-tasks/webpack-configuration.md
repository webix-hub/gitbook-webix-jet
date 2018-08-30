# Configuring Webpack

There are some cases when you might want to add some extra configuration for your app. You can change the default Webpack configuration and get the expected result.

## Configuring Webix Jet to Work with a Backend Server

{% hint style="info" %}
These instructions are for the **development stage**. For production, [see this](deploying-and-testing.md).
{% endhint %}

Let's look at an example of configuring Webix Jet to work with the Apache Tomcat server.

**Prerequisites:**

1. There is a Java app on your Tomcat server, configured to your needs.
2. There is a Webix Jet app (you can use the [jet-start package](https://github.com/webix-hub/jet-start)).

You need to let the Webix Jet app access the Java app. Set the **proxy** path in **webpack.config.js**. Replace this:

```js
devServer:{
    stats:"errors-only"
}
```

with this:

```js
devServer:{
    stats:"errors-only",
    proxy: {
        "/server":{
            target: 'http://localhost:9200',
            pathRewrite: {"^/server" : ""}
        }	
    }
}
```

"http://localhost:9200" is the web path to the Java server side.

This configuration will make it possible to set the path to the server as:

```js
// models/mydata.js
export const mydata = new webix.DataCollection({        
    url:"/server/mydata"
})
```

This will load the data from "http://localhost:9200/mydata".

Both servers can be run separately and at the same time. The Webix Jet app will be able to communicate with the server-side code as they both are run by the same server.

## Multiple Start Files

By default, the app is built with one start file \(_admin.js_ in this example\):

```javascript
/* webpack.config.js */
...
var config = {
    entry: "./sources/admin.js",
    output: {
        //...
        filename: "admin.js"
    },
    ...
}
...
```

To create multiple entry files, pass an object to **entry** in config. For output filenames, use the _\[name\]_ substitution to ensure that each file has a unique name:

```javascript
/* webpack.config.js */
{
    entry: {
        admin: "./sources/admin.js",
        orders: "./sources/orders.js"
    },
    output: {
        //...
        filename: "[name].js"
    }
}
```

## Turning Off Localization

If you do not want to localize the app and do not want to create an empty **locales** folder, you can change the Webpack config.

By default, Webpack tries to resolve the locales in *sources/locales*. Delete the `jet-locales` alias:

```javascript
/* webpack.config.js */
var config = {
    ...
    resolve: {
        extensions: [".js"],
        modules: ["./sources", "node_modules"],
        alias:{
            "jet-views":path.resolve(__dirname, "sources/views"),
            "jet-locales":path.resolve(__dirname, "sources/locales") // !
        }
    },
    ...
}
```

Next tell Webpack to ignore `jet-locales` while compiling the app. Use the [**IgnorePlugin**](https://webpack.js.org/plugins/ignore-plugin/) for this:

```javascript
/* webpack.config.js */
...
plugins: [
	new MiniCssExtractPlugin({
		filename:"[name].css"
	}),
	new webpack.DefinePlugin({
		VERSION: `"${pack.version}"`,
		APPNAME: `"${pack.name}"`,
		PRODUCTION : production,
		BUILD_AS_MODULE : (asmodule || standalone)
	}),
	new webpack.IgnorePlugin(/jet-locales/)	// !
]
```

Now the app will be compiled without the locales.

## Changing Paths tor Locales and Views

By default, views and locales are stores in **sources/views** and **sources/locales** correspondingly. If you want your app structure to be different, you can change the paths to views and locales in the Webpack config file:

```javascript
/* webpack.config.js */
var config = {
    ...
    resolve: {
        extensions: [".js"],
        modules: ["./sources", "node_modules"],
        alias:{
            "jet-views":path.resolve(__dirname, "sources/components"),	// !
            "jet-locales":path.resolve(__dirname, "sources/languages") 	// !
        }
    },
    ...
}
```

