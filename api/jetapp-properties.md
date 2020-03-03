# JetApp Properties

### config

Stores app configuration settings (both default and the ones you added to the JetApp constructor).

```javascript
// app.js
import { JetApp } from "webix-jet";
export class App extends JetApp {
	constructor(config) {
        const defaults = {
			version: VERSION,
			debug: DEBUG,
			start: "/top",
		};

        super({ ...defaults, ...config });
    }
}

// index.html
var app = new App({
    url: "//localhost:3200/",
});
app.render(document.body);

// views/top.js
export default class TopView extends JetView {
    config(){
        const appconf = this.app.config;
        /*
            {
                "name":"App",
                "version":"1.0",
                "start":"/top",
                "debug":true,
                "url":"//localhost:3200/"
            }
         */
    }
}
```

### ready

A promise that resolves when the app is rendered.

```javascript
// index.html
var app = new App();
app.render(document.body);
app.ready.then(() => {
    /* do something */
});
```