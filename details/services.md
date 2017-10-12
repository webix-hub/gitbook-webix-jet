# Services

JetApp provides the means to initialize services. To set a service, call the **setService** method:

```js
init() => {
	this.app.setService("masterTree", {
		getSelected : () => this.getRoot().getSelectedId()
	})
```

*getSelected* of the *masterTree* service returns the ID of the selected node of a tree. To call getSelected, use the **getService** method:

```js
this.app.getService("masterTree").getSelected();
```
