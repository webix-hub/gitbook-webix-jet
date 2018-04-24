# Creating Complex Webix Widgets with Webix Jet

For simple widgets that wrap some third party library components, you can create custom widgets with webix.protoUI and base them on webix.ui.view as a base

For medium widgets that contain a few widgets, you can create custom components with webix.protoUI and base them on webix.ui.layout, then define the layout configuration in the **$init** method of the new widget.

For complex cases, when you have multiple layouts, dynamic views, complex data etc., you can use Webix Jet.
You can create an app by Webix Jet rules and convert it to a new widget:

```js
class NewWidgetApp extends JetApp {
   ...
}

webix.protoUI({
   name:"new-widget",
   app: NewWidgetApp
}, webix.ui.jetapp);
```

Later this widgets can be used like an ordinary Webix widget:

```js
webix.ui({ view:"new-widget" });
```

### Important

If you plan to use Jet app as a widget

1.
Do not use any hardcoded "id" values, as it will prevent initializing more than one instance of the widget.
Use localId and this.$$ or queryView to locate the widgets inside of view. 

2. 
Use services instead of models.
Models are shared between all instances of the app ( widget ), so with shared model change in one instance of the widget
will be reflected in all other widgets. If you want to have a separate state ( which is a common use-case )
you need to has unique model per widget. 

Instead of 

```js
import {data} from "models/records";
```

use

```js
// models/records.js
function init(){
  const data = new webix.DataCollection({});
  
  return { data };
}
```

```js
// app.js
import records from "models/records";

class MyApp extends JetApp {
  constructor(config){
    super(config);
    
    this.setService("records", records()); 
  }
}
```

```js
// view.js
class MyView(){
   init(){
      this.$data = this.app.getService("records");
   }
}
```