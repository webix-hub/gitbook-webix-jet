# Asynchronous (Promised) Views

Data is usually stored in a database on the server side. If the UI is built much faster than the data is loaded, it's a good idea to make use of promises to load the data. To make the UI wait for data, you can make an asynchronous request to a PHP script and give the UI a promise of data. Then the app will wait for data from a database, and only after a promise resolves, it will render the view with the data.

For example, there is a chart on the start page, and you need to define the colors of its lines, specified in the **series** parameter. Colors can be stored as inline data:

```js
//views/statistics.js
import {JetView} from "webix-jet";

export class StatisticsView extends JetView {
    config() {
        return {
            view:"chart",
            series:[{ value:"#sales#", color:"#1293f8"},{value:"#sales2#", color:"#66cc00"}],
            url:"server/colors.php",
            ...//the rest of config
        }
    }
});
```

In practice, however, some UI configuration settings can be stored in a database. For example, you may want to store colors in a DB to allow the end-user to change them. In this case, a module can return a **promise** of the UI instead of the UI configuration.

## webix.ajax

#### A Promise Returned by **config** of a Class View

Let's use **webix.ajax** that makes an asynchronous request to a PHP script and returns a promise. After the promise resolves, the response is passed to a callback in **then**:

```js
export class StatisticsView extends JetView {
    config() { 
        return webix.ajax("server/colors.php").then(function(data){
            /* view creation */
            data = data.json();
            return {
                view:"chart",
                series:[
                    { value:"#sales#", color:data[0].color},
                    { value:"#sales2#", color:data[1].color}
                ]
            }
        });
    }
};
```

The **webix.ajax\(\)** call sends an asynchronous request to the *server/colors.php* script on the server and returns a promise of data instead of real data. First, all the data should come to the client side and only after that the final view configuration will be constructed and the view will be rendered.

#### A Simple View as a Promise

The same view can be defined in a simpler way. You can define it as a promise of a view object:

```js
export default webix.ajax("server/colors.php").then(function(data){
    /* view creation */
    data = data.json();
    return {
        view:"chart",
        series:[
            { value:"#sales#", color:data[0].color},
            { value:"#sales2#", color:data[1].color}
        ]
    };
}
```

Have a look at a list view defined as a promise:

```js 
const data = webix.ajax("data").then(res => {
	return {
		view:"list", options:res.json()
	}
});
export default data;
```

More explicitly, the same list view can be defined like this:

```js
export default new Promise((res, rej) => {
	webix.ajax("data", function(text, res){
		const ui = {
			view:"list", options:res.json()
		};
		res(ui);
	})
});
```

## webix.promise Interface

Webix offers an interface for working with promise objects. [webix.promise](https://docs.webix.com/api__refs__promise.html) features a set of methods that duplicate [Promise object methods](https://github.com/zolmeister/promiz). **webix.promise** allows treating the result of asynchronous operations without callbacks.

#### Simple View that Returns a Promise

**defer** of **webix.remote** creates a new instance of a promise object. **resolve** creates and resolves a promise with a specified value, for example, a Webix widget.

Have a look at a simple example of a factory function that returns a promise that resolves in a second: 

```js
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

The promise resolves with a template in this case. The delay is done purely for the demo.

#### Promises in Class Views

The same promises can be returned by **config** of a class view:

```js
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

[Check Out the Demo >>](https://github.com/webix-hub/jet-demos/blob/master/sources/promises.js)

## **data.waitData**

that is used for data components, such as DataCollection, List, Tree, DataTable, etc.;

