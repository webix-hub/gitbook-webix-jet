# <span id="contents">Webpack Config</span>

There are some cases when you might want to change the default Webpack configuration.

- [Multiple Start Files](#multiple_start)
- [Turning Off Localization and Views](#turn_off)

### [<span id="multiple_start">Multiple Start Files &uarr;</span>](#contents)

By default, the app is built with one start file (*admin.js* in this example):

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

To create multiple entry files, pass an object to **entry** in config. For output filenames, use the *[name]* substitution to ensure that each file has a unique name:

```js
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

### [<span id="turn_off">Turning Off Localization and Views &uarr;</span>](#contents)

If you aren't planning to localize the app, there is a way to do it without creating an empty folder for locales. Without changes in Webpack config, you would have to do that. Webpack config has the **resolve** property, the options for locating modules. It tells Webpack where to look for views and locales.

By default, Webpack loads views from the **views** folder like this: *require("jet-views/"+name)*. *resolve.alias* settings in webpack.config makes it so:

```js
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

An alias is used instead of hardcoding the *"/views"* path to make this part configurable. If necessary, you can change webpack.config and define new folders for loading views.

For example, you can change the path in *"jet-locales"*.