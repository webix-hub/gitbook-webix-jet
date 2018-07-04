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

## Turning Off Localization and Views

If you aren't planning to localize the app, there is a way to do it without creating an empty folder for locales. Without changes in Webpack config, you would have to do that. Webpack config has the **resolve** property, the options for locating modules. It tells Webpack where to look for views and locales.

By default, Webpack loads views from the **views** folder like this: _require\("jet-views/"+name\)_. _resolve.alias_ settings in webpack.config makes it so:

```javascript
/* webpack.config.js */
var config = {
    ...
    resolve: {
        extensions: [".js"],
        modules: ["./sources", "node_modules"],
        alias:{
            "jet-views":path.resolve(__dirname, "sources/views"),
            "jet-locales":path.resolve(__dirname, "sources/locales") //change me
        }
    },
    ...
}
```

An alias is used instead of hardcoding the _"/views"_ path to make this part configurable. If necessary, you can change **webpack.config** and define new folders for loading views.

For example, you can change the path in _"jet-locales"_.

