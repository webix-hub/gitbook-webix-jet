## Models

Data can be loaded by code in views, however it's also possible to create separate modules for data as well. While views contain the code of interface components, models can be used to control the data. This is useful in some cases, for example:

- shared data
- component specific data

For example, if you want to load some hard-coded data into a data table, create a *records* model in the corresponding folder:

```js
/* models/records.js */
export const data = new webix.DataCollection({ data:[
	{ id:1, title:"The Shawshank Redemption", year:1994, votes:678790, rating:9.2, rank:1},
	{ id:2, title:"The Godfather", year:1972, votes:511495, rating:9.2, rank:2},
	{ id:3, title:"The Godfather: Part II", year:1974, votes:319352, rating:9.0, rank:3},
	{ id:4, title:"The Good, the Bad and the Ugly", year:1966, votes:213030, rating:8.9, rank:4},
	{ id:5, title:"My Fair Lady", year:1964, votes:533848, rating:8.9, rank:5},
	{ id:6, title:"12 Angry Men", year:1957, votes:164558, rating:8.9, rank:6}
]});
```

It is highly recommended to parse data in **init**, not in **config**:

```js
/* views/data.js */
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