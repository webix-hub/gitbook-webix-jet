# JetView API

### use



### show

```js
/* sources/views/toolbar.js */
const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: () => {
                this.show(this.getValue().toLowerCase());
            }
    }]
};
export default Toolbar;
```

### ui


<!-- from admin app orders.js
export default class OrdersView extends JetView{
	config(){
		return layout;
	}
	init(view){
		view.queryView({ view:"datatable" }).parse(data);
		this._form = this.ui(orderform);
	}
}
 -->