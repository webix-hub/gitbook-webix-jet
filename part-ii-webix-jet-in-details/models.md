# Models

View modules return the UI configuration of the components. You can put data loading logic in views, which is relevant for prototyping and considered bad practice for real-life apps.

Good practice is to separate data loading logic from UI configuration and put it in separate modules - **models**. A model stores all functionality related to some data entity. This division of UI and data is an advantage because if the data changes, all you have to do is change the data model. There is no need to modify all the views that use the data.

In the data model, you can define:

* a DataCollection that will load the data and manage saving;
* a method that will fetch the data from backend.

There are several types of models in Webix Jet:

* shared models
* dynamic models
* remote models
* services for data
* models with webix.remote

## 1. Shared Data

For _relatively small data_ used in _many components_, you can load data into Webix DataCollection. This way, you make a single request to the server, store the data on the client side and sync it with the necessary views.

### Loading

You can export either the whole collection or an accessor function that will return it.

```javascript
// models/records.js
export const records = new webix.DataCollection({ 
    url:"data.php"
});
//or
const records = new webix.DataCollection({ 
    url:"data.php"
});
export function getRecords(){ return records; };
```

### Saving

A collection will handle _save_ operations as well. You need to provide a **save** URL for a collection, so that it can send all updates made from views to the server:

```javascript
// models/records.js
export const records = new webix.DataCollection({ 
    url:"data.php",
    save:"data.php"
});
```

### Data Parsing

To use the data in a component, you can parse it. You must parse data in **init\(\)**, not in **config\(\)** \(leave _config\(\)_ for the UI\). Have a look at the example with a datatable:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {records} from "models/records";

export default class DataView extends JetView{
    config: () => {
        view:"datatable", autoConfig:true, editable:true
    }
    init(view){
        view.parse(records);
    }
}
```

All the changes made in the datatable are saved to the server.

### Models for Options of Select Boxes

You can use models to load options of select boxes, e.g.:

```javascript
// views/start.js
import {options} from "models/options";
import {JetView} from "webix-jet";

export default class StartView extends JetView {
    config(){
           return {
               view:"form", elements:[
                { view:"combo", options:{ data:options }}
            ]
        };
    }
});
```

And for options of Datatable select editors:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {options} from "models/options";

export default class DataView extends JetView{
    config(){
        return {
            view:"datatable", editable:true, columns:[        
                { id:"categoryId",  editor:"richselect", collection:options }
            ]
        }
    }
}
```

### Syncing Components to DataCollection

You can also sync a _data component_ inside a Jet view with a DataCollection. Let's sync the datatable with records:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {records} from "models/records";

export default class DataView extends JetView{
    config: () => {
        view:"datatable", autoConfig:true, editable:true
    }
    init(view){
        view.sync(records);
    }
    removeRecord(id){
        records.remove(id);
    }
}
```

Mind that if you synced a data component to a DataCollection, you must _perform **add/remove** operations on the master collection_, while the synced view will reflect these changes automatically. Slave views can only update the master.

### Shared Data Transport

To return several types of data and distribute data chunks to different views, a shared data transport can be used.

For example, this is the data model for a grid:

```javascript
//models/griddata.js
import {sharedData} from "models/shareddata";

const gridData = new webix.DataCollection();
gridData.parse(sharedData("grid"));

export gridData;
```

And here is the "shareddata" file that communicates with the shared data feed and can provide different data chunks for different views, including the grid:

```javascript
//models/shareddata.js
var data = webix.ajax("some.json").then(a => a.json());
export function sharedData(name){
    return data.then(a => {
        switch (name){
            case "grid":
                return a.grid;
            default:
                return [];
        }
    });
}
```

Each view has its own model that stores data and all related API and uses _shareddata_ for data loading.

### 2. Dynamic Data for Big Data

As browser resources are limited, big collections should not be stored on the client side. Dynamic model is for _big data_ \(less than 10K records\) _used only once_ that must not be cached.

### Loading

The data can be loaded from a server directly with an AJAX request:

```javascript
// models/records.js
export function getData(){
    return webix.ajax("data.php");
}
```

It can be a service, a script, a function, etc. and it should return either a data object/array or a promise that will be resolved with the needed data.

To parse data, pass the return value of _getData_ to _view.parse_:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {getData} from "../models/records";

export default class DataView extends JetView{
    config: () => { 
        view:"datatable", autoConfig:true
    }
    init(view){ 
        view.parse(getData());
    }
}
```

You can also load data from local storage:

```javascript
// models/records.js
export function getData(){
    return webix.storage.local.get("data");
}
```

### Saving

To save data, you must provide a pattern for saving. Let's add one more function to the _records_ model:

```javascript
// models/records.js
export function getData(){
    return webix.ajax("data.php");
};
export function saveData(id, operation, data){
    if (operation is "update") //"add", "delete"
        return webix.ajax().post("data.php", data);
    if (operation =="add")
        // ...
};
```

The **saveData\(\)** function provided as the **save** property of a data widget, enables DataProcessor, which will call this function for all additions, removals and updates. Let's define a way to save data in _init\(\)_ of a view:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {getData, saveData} from "models/records";

export default class DataView extends JetView{
    config: () => { 
        view:"datatable", autoConfig:true, editable:true
    }
    init(view) {
        view.parse(getData());
        view.define("save", saveData);
    }
}
```

Loading and saving data can also be in **config** of the view module, if needed:

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {getData, saveData} from "models/records";

export default class DataView extends JetView{
    config: () => {
        view:"datatable", autoConfig:true,
        url: getData,
        save: saveData
    }
}
```

## 3. Remote Models for Huge Data

In case data is really huge, you can drop the whole concept of separating data from UI and rely on the dynamic loading of Webix components \(Datatable, Dataview, List, Tree\). Data will be loaded in portions when needed. For that, load data in the view configuration. You can do it with the **url** property. For saving data, use the **save** property.

```javascript
// views/data.js
import {JetView} from "webix-jet";

export default class DataView extends JetView{
    config() => {
        view:"datatable", autoConfig:true,
        url:"data.php",
        save:"data.php"
    }
}
```

Data can also be dynamically loaded with the **load** method:

```javascript
// views/data.js
import {JetView} from "webix-jet";

export default class DataView extends JetView{
    config(){
        var ui = { view:"datatable", autoConfig:true };
        ui.load("data.php");
        return ui;
    }
}
```

Webix dynamic loading pattern allows loading data in portions, provided that your backend returns the response as: _{ data:\[\], total\_count:1000, pos:100}_. When the component is scrolled \(or tree branches are opened\) the component itself can send the request to the server for a new portion of data using the defined data url.

## Shared vs Dynamic vs Remote models

Let's recap main differences of the three ways to load and save data:

| Model | Data Size | Usage |
| :--- | :--- | :--- |
| Shared | Small | Many times |
| Dynamic | Big | Once |
| Remote | Huge | Handy for prototyping |

## 4. Services as Data Sources

You can use services instead of models as data sources. Suppose there is a list of customers and a grid that displays records on a selected customer. Here is how you can use a service that returns the ID of a selected list item:

```javascript
var id = service.getSelected();
var data = records.getData(id);
```

A better and shorter way is:

```javascript
var data = service.getNomenclature();
```

## 5. Using Webix Remote with Webix Jet

You can use [webix.remote](https://docs.webix.com/desktop__webix_remote_php.html) instead of sending AJAX requests. You must have a server-side script with a class for getting the data:

```php
//api.php
class Nomencl {
    public function getData(id) { /*..*/ }
}
$api->setClass("nm", new Nomencl());
```

The class with all its methods is used by _webix.remote_ in a model:

```javascript
// models/nomencl.js
export default webix.remote.nm;
```

_webix.remote_ can also be used to create a DataCollection:

```javascript
// models/nomencl.js
var data = new DataCollection({
    url: webix.remote.nm.getData
})
```

Models with _webix.remote_ can be imported and parsed it into views:

```javascript
// views/data.js
import records from "models/nomencl";
...
init(view){
    view.parse(records.getData(id));
}
```

**webix.remote** is better then an AJAX request for several reasons:

_1. You don't need to serialize data after loading._

Compare the results of these two requests:

```javascript
var data1 = webix.ajax("data/nomencl/154");         //"{"id":154,"name":"John"}"
var data2 = webix.remote.nm.getNomenclature(154);    //{id:154,name:"John"}
```

After receiving the response of the AJAX request, you have to call `JSON.parse(data1)` to turn a string into an object. The response of the second request is already an object.

_2. Requests with_ webix.remote _are safer due to CSRF-security._

_3. Several requests are sent as one, which makes operations faster._

## Further reading

For more details on services, read:

* [View Communication](view-communication.md)

