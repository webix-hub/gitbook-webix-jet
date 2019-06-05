# Importing Webix as Module

We strongly recommend you to include Webix as a script, e.g.:

```html
<script type="text/javascript" src="//cdn.webix.com/edge/webix.js"></script>
<link rel="stylesheet" type="text/css" href="//cdn.webix.com/edge/webix.css">
```

But if you really need to include Webix as a module, you can do this in two ways.

## Building a Huge Bundle of Webix and the App

{% hint style="warning" %}
Please note that this way in not recommended, as the resulting file is relatively big and will work better as a separate script, without bundling in the single *app.js*.
{% endhint %}

1\. Make Webix available for all includes by adding the following section in **webpack.config.js**:

```js
plugins: [
	new webpack.ProvidePlugin({
		// SET PATH TO WEBIX HERE
		webix: path.join(__dirname, "node_modules", "@xbs", "webix-pro")
	}),
	// ...
]
```

2\. Import Webix:

```js
import { JetApp } from "webix-jet";
import * as webix from "webix"; // use script !!!

export default class MyApp extends JetApp{
    constructor(config){
        const defaults = {
            id: APPNAME,
            version: VERSION,
            debug: !PRODUCTION,
            start: "/top",
            webix: webix
       	};
        super({ ...defaults, ...config });
    }
}
```

## Alternative Solution

The alternative solution will be to use the **script loader** for **webix.js**.

1\. Add **script-loader** to the dependencies in **package.json**:

```json
"dependencies": {
    "script-loader": "^0.7.2",
    "webix": "^6.3.5",
    "webix-jet": "^2.0.4"
}
```

2\. Add the following lines in **webpack.config.js**:

```js
module.exports = {
    module: {
        rules: [
            {
                test: /\@xbs\/webix-pro\/webix\.js$/,
                use: [ 'script-loader' ]
            }
        ]
    }
}
```

This solution will use the script-loader for **webix.js**, which will load **webix.js** as a global script, so it will be accessible to all the code. It is the same as including a script tag with **webix.js** inside of **index.html**.

[Check out the demo >>](https://github.com/webix-hub/jet-start/tree/import-bundle)
