## Services

init() => {
	this.app.setService("masterTree", {
		getSelected : () => this.getRoot().getSelectedId()
	})


this.app.getService("masterTree").getSelected();

