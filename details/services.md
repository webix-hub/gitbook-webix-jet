# Services



```js
init() => {
	this.app.setService("masterTree", {
		getSelected : () => this.getRoot().getSelectedId()
	})
```

```js
this.app.getService("masterTree").getSelected();
```
