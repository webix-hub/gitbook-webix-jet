## Promised views

Sometimes data can be loaded directly from a JSON file or a variable, this is so-called hard coded data. But data is more often stored in a database on the server side. It's a good idea to make use of promises to load the data. This is especially relevant in case the UI is built much faster than the data is loaded. To make the UI wait for data, you can make an asynchronous request to a PHP script and give the UI a promise. Thus an app will wait for data from a database, and only after a promise resolves, it will render the view with the data.

For example, there's a chart on the start page and you need to define the colors of its lines, specified in the **series** parameter:

```js
//views/statistics.js
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

However, in practice some configuration settings in our UI can be stored in the database. For example in the above snippet we may want to store colors in DB to allow their customization by the end user. In such case, a module can return a promise of UI instead of UI configuration.

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

The **webix.ajax\(\)** call sends an asynchronous request to the _server/colors.php_ script on the server and returns a promise of data instead of real data. First, all the data should come to the client side and only after that the final view configuration will be constructed and the view will be rendered.

There are several ways to implement asynchronous data loading:

* **webix.ajax** that makes an asynchronous request to a PHP script and shows its response through a callback function;
* **data.waitData** that is used for data components, such as DataCollection, List, Tree, DataTable, etc;
* **webix.promise** that allows treating the result of asynchronous operations without callbacks.



