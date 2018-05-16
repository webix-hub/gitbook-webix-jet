# Webpack Configuration

There are some cases when you might want to change the default Webpack configuration.

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

An alias is used instead of hardcoding the _"/views"_ path to make this part configurable. If necessary, you can change webpack.config and define new folders for loading views.

For example, you can change the path in _"jet-locales"_.

