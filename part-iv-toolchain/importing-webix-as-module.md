# Importing Webix as Module

We strongly recommend you to include Webix as a script, e.g.:

```html
<script type="text/javascript" src="//cdn.webix.com/edge/webix.js"></script>
<link rel="stylesheet" type="text/css" href="//cdn.webix.com/edge/webix.css">
```

But if you really need to include Webix as a module, you can do this in two ways.

## Building a Huge Bundle of Webix and the App

{% hint style="warning" %}
Please note that this way is not recommended, as the resulting file is relatively big and will work better as a separate script, without bundling in the single *app.js*.
{% endhint %}

1\. Make Webix available for all includes by adding the following section in **webpack.config.js**:

```javascript
plugins: [
	new webpack.ProvidePlugin({
		// SET PATH TO WEBIX HERE
		webix: path.join(__dirname, "node_modules", "@xbs", "webix-pro")
	}),
	// ...
]
```

2\. Import Webix:

```javascript
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
