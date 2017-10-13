# Webpack config

There are some cases when you might want to change the default webpack configuration.

- multiple start files

By default, the app is built with one start file:

```js
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

To create multiple entry files, pass an object to **entry** and use the *[name]* substitution for output filenames to ensure that each file has a unique name:

```js
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

- Turning off Localization and Views

If you aren't planning to localize the app, there's a way to do it without creating and empty folder for locales. Without changes in webpack config, you would have to do that to get the app compiled. webpack config has the **resolve** property that presents options affecting the resolving of modules. Delete *"jet-locales"* key pairs from **resolve.alias**.

```js
var config = {
    ...
    resolve: {
        extensions: [".js"],
        modules: ["./sources", "node_modules"],
        alias:{
            "jet-views":path.resolve(__dirname, "sources/views"),
            "jet-locales":path.resolve(__dirname, "sources/locales") //delete me
        }
    },
    ...
}
```

Besides, if you do not want webpack to look for views in the **sources/views** folder, modify the *"jet-views"* key pair as well. And you can change the *"./sources"* directory for your modules as well.