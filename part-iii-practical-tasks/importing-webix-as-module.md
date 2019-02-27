# Importing Webix as Module

We strongly recommend you to include Webix as a script, e.g.:

```html
<script type="text/javascript" src="//cdn.webix.com/edge/webix.js"></script>
<link rel="stylesheet" type="text/css" href="//cdn.webix.com/edge/webix.css">
```

But if you really need to create a single bundle with Webix and your app code, this is the right way to import Webix:

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