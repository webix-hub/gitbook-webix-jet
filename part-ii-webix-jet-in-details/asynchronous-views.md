# Asynchronous Views

Data are usually stored on the server side. If data are loaded asynchronously, you need to wait until they are available to perform data-related operation with views, e.g. select a record. If the UI is built much faster than data are loaded, it is a good idea to make use of promises to load the data. The app will wait for data from the server, and only after a promise resolves, it will render the view.

Promises are returned by all Ajax requests within Webix:

* [webix.ajax\(\)](https://docs.webix.com/api__refs__ajax.html), which is resolved when an Ajax request is completed,
* [data.waitData](https://docs.webix.com/api__link__ui.proto_waitdata_other.html), which is resolved when a data component is populated with remote data,
* [webix.promise](https://docs.webix.com/api__refs__promise.html), an interface for working with promise objects.

## webix.ajax\(\)

### A Promise Returned by _config\(\)_ of a Class View

If your client-side depends on server-side data, you can fetch the data asynchronously and return a promise of them in the [config\(\)](views-and-subviews.md#config) lifetime handler of a Jet view. A promise can be returned by [webix.ajax\(\)](https://docs.webix.com/api__refs__ajax.html). After the promise resolves, the response is passed to a callback in **then\(\)**.

For example, let's load chart configuration with **webix.ajax\(\)**:

```javascript
//views/statistics.js
import {JetView} from "webix-jet";

export class StatisticsView extends JetView {
    config() { 
        return webix.ajax("data/colors").then( data => {
            const colors = data.json();
            const chart = { view:"chart", series:[]};
            for (let i = 0; i < colors.length; i++)
                chart.series.push({
                    value:"#"+i+"#", color:colors[i]
                });
            return chart;
        });
    }
}
```

**webix.ajax\(\)** sends a request to _server/colors.php_ and returns a promise of data instead of real data. First, all the data come to the client side and only after that the final view configuration will be constructed and the view will be rendered.

### A Simple View as a Promise

You can define a view as a promise of a view object:

```javascript
// views/chart.js
export default webix.ajax("server/colors.php").then(function(data){
    /* view creation */
    const colors = data.json();
    const chart = { view:"chart", series:[]};
    chart.series.push({
        value:"#"+i+"#", color:colors[i]
    });
    return chart;
}
```

Let's load data into a list inside a simple promised view:

```javascript
// views/data.js
const data = webix.ajax("data").then(res => {
    return {
        view:"list", data:res.json()
    }
});
export default data;
```

More explicitly, the same list view can be defined like this:

```javascript
// views/data.js
export default new Promise((res, rej) => {
    webix.ajax("data", function(text, res){
        const ui = {
            view:"list", data:res.json()
        };
        res(ui);
    })
});
```

## data.waitData

[waitData](https://docs.webix.com/api__link__ui.proto_waitdata_other.html) is used to catch the moment when data are loaded into data components, such as DataCollection, List, Tree, DataTable, etc.

```javascript
// views/data.js
import {JetView} from "webix-jet";
import {data} from "models/records";

export default class DataView extends JetView {
    config(){
        return { view:"datatable", autoConfig:true };
    }
    init(view){
        view.sync(data);
        data.waitData.then(() => {
            view.select(view.getFirstId());
        });
    }
}
```

_data_ is a model with Webix DataCollection, e.g.:

```javascript
// models/records.js
export const data = new webix.DataCollection({
   url:"/some/records"
});
```

## **webix.promise**

Webix offers an interface for working with promise objects. [webix.promise](https://docs.webix.com/api__refs__promise.html) features a set of methods that duplicate [Promise object methods](https://github.com/zolmeister/promiz).

### Waiting for Data from Multiple Sources

Promises are great when you need to wait until _several data sources_ are available on the client.

For example, these are data returned by two models, the first dataset is linked to the second via IDs:

```javascript
// models/data.js
[
    { id:1, title:"Trigger-happy", categoryId:1 },
    { id:2, title:"In the Mist", categoryId:2 },
    { id:3, title:"Broken Mirrors", categoryId:3 }
]

// models/categories.js
[
    { id:1, value:"Crime"},
    { id:2, value:"Thriller"},
    { id:3, value:"Drama" }
]
```

Use **webix.promise.all\(\)** to wait till both data models are loaded and then do something with them:

```javascript
// views/details.js
import {JetView} from "webix-jet";
import {data} from "models/records";
import {categories} from "models/categories";

export class DetailsView extends JetView {
    config(){
        return { template:"#title#. #category#" };
    }
    urlChange(view, url){
        webix.promise.all([
            data.waitData,
            categories.waitData
        ]).then(()=>{
            let id = url[0].params.id;              //URL: "top?id=2/details"
            let item = webix.copy(data.getItem(id));
            item.category = categories.getItem(item.categoryId).value;
            view.parse(item);
        });
    }
}
```

**webix.promise** has more useful methods, e.g.:

* [defer\(\)](https://docs.webix.com/api__promise_defer.html) that creates a promise,
* [resolve\(\)](https://docs.webix.com/api__promise_resolve.html) that creates and resolves a promise.

### A Simple View that Returns a Promise

Have a look at a simple example of a factory function that returns a promise that resolves with a template in a second. The delay is done purely for the demo.

```javascript
// views/promised.js
const promised = () => {
    var t = webix.promise.defer();
    setTimeout(function(){
        t.resolve({ template:"Resolved by promise" });
    }, 1000);
    return t;
};
export default promised;
```

### Promises in Class Views

**webix.promise** can also return promises from **config\(\)** of a class view:

```javascript
// views/top.js
import {JetView} from "webix-jet";

class TopView extends JetView {
    config(){
        var res = webix.promise.defer();
        var ui = {
            template:"Resolved by a promise"
        };
        setTimeout(function(){
            res.resolve(ui);
        }, 1000);
        return res;
    }
}
```

[Check out the demo &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/promises.js)

