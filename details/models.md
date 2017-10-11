## Models

Data can be loaded inside view modules. It's also possible to create separate modules for data. While views contain the code of interface components, models can be used to control the data. This is useful in some cases, for example:

- shared data
- component-specific data

If you want to load some hard-coded data into a data table, create a *records* model in the *models* folder:

```js
/* sources/models/records.js */
export const data = new webix.DataCollection({ data:[
	{ id:1, title:"The Shawshank Redemption", year:1994, votes:678790, rating:9.2, rank:1},
	{ id:2, title:"The Godfather", year:1972, votes:511495, rating:9.2, rank:2},
	//...
]});
```

Next, you need to parse the data from the DataCollection into the datatable. It is highly recommended to parse data in **init**, not in **config**:

```js
/* sources/views/data.js */
import {JetView} from "webix-jet";
import {data} from "models/records";

export default class DataView extends JetView{
	config(){
		return { view:"datatable", autoConfig:true };
	}
	init(view){
		view.parse(data);
	}
}
```

You can also load data into a DataCollection from the server:

```js
/* sources/models/records.js */
export const data = new webix.DataCollection({ 
	url:"data.php"
});
```
