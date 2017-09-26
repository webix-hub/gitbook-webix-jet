## Referencing Views from Webix UI Events

In the new version of Webix Jet, there is a convenient reference to a view from Webix UI events with the **this** pointer. Consider the following use-cases.

1. Reference to the view

If you want to change the URL from controls of the view, reference the view with **this.$scope**: 

~~~js
/* views/toolbar.js */
export const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: function() {
                this.$scope.show(this.getValue())
            }
    }]
};
~~~

If you prefer a shorter syntax, let the handler be an arrow function. Then simply use **this** for view reference:

~~~js
/* views/toolbar.js */
export const Toolbar = {
    view: "toolbar",
    elements: [
        { view: "label", label: "Demo" },
        { view: "button", value:"Details",
            click: () => {
                this.show(this.getValue())
            }
    }]
};
~~~

2. Reference to the app

**this.$scope.app** references the app that encloses the view. This is useful if you want to rebuild the app UI. Let's shorten the syntax with an arrow function again:

~~~js
/* views/toolbar.js */
export class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" },
                { view: "button", value:"Details",
                    click: () => {
                        this.app.show("/" + this.getValue())
                    }
            }]
        };
    }
}
~~~

3. Reference to the root UI element of the view

Suppose you have a view with a form and you want to validate its input when users click **Submit**. To refer to the form itself and not the whole view, use **this.$scope.getRoot()**. It references the root UI element returned by config. In an arrow function the reference is **this.getRoot()**:

~~~js
/* views/form.js */
export class FormView extends JetView{
	config(){
		return { 
			view:"form", elements:[
				{ view:"text", name:"email", required:true, label:"Email" },
				{ view:"button", value:"save", 
                    click: () => {
                        if (this.getRoot().validate())
                            this.show("Details");
                    } 
                }
			]
		};
	}
}
~~~

4. Using plugins

Form validation can be implemented in one more way - with a plugin **UnloadGard**. The plugin reacts to an attempt of changing the URL. The syntax for using a plugin is *this.use(plugin,handler)*. Use takes two parameters:
- a plugin name
- a function that will handle the **Unload** event

Now validation is moved from the **Save** button handler to the plugin handler:

~~~js
class FormView extends JetView {
	config(){
		return { 
			view:"form", elements:[
				{ view:"text", name:"email", required:true, label:"Email" },
				{ view:"button", value:"save", click:() => this.show("Details") }
			]
		};
	}
	$init(ui, url){
		this.use(JetApp.plugins.UnloadGuard, () => {
			if (this.getRoot().validate())
				return true;

			return new Promise((res, rej) => {
				webix.confirm({
					text: "Are you sure ?",
					callback: a => a ? res() : rej()
				})
			});
		});
	}
}
~~~

If the input isn't valid, the function returns a promise with a dialogue window. Depending on the answer, the promise either resolves and the Guard lets the user go to the next view, or it rejects. No pasaran.

For more details about plugins, check out a [dedicated section](/).

5. Referencing nested views and controls

You already know how to change the URL by controls. Now have a look how the state of controls can be changed by the URL. For example, if you have a toolbar with a segmented button that is used to switch between two views:

~~~js
class ToolbarView extends JetView {
    config() {
        return {
            view: "toolbar",
            elements: [
                { view: "label", label: "Demo" }, 
                {
                    view: "segmented", localId: "control", options: ["Details", "Dash"],
                    click: function() {
                        this.$scope.app.show("/Demo/" + this.getValue())
                    }
            }]
        };
    }
}
~~~

This works fine, however, if you switch to Dash and reload the page, the segmented button won't be turned to **Dash**. In more complex apps this behavior might be confusing. To fix this, use **this.$$("localId")** to reference the button and set its value. **this.$$("localId")** can be used to reference nested views and controls by their global or local IDs.

~~~js
class ToolbarView extends JetView {
    config() {
        /* same config */
    }
    $init(ui, url) {
        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
}
~~~

6. Attaching events

To attach events, use **this.on()**, which is an alias of **attachEvent()**. For more details, consider the [dedicated section](/events).

7. Referencing UI

To refer to the UI of the view, use **this.ui()** that is a substitute for **webix.ui()** familiar to those who aren't new to Webix.