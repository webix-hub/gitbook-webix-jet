# Services

Services are one more means of view communication besides [the event bus and methods](events.md). Methods are preferable when you want to connect views with their subviews. If views aren't related to each other, e.g. if you want to connect two subviews of the same view, you cannot use methods. This is where services help. You can create a service connected to one of the views, and other views can communicate with the view through the service. In short, a service serves as shared functionality.

The same communication can be implemented with events, but this is much more complex. With ES6, the problem can also be solved with creating and requiring a module. Services are better, because if you create two apps that require the same module, there's only one instance of the shared code. With services, a new instance of a service is created each time.

## Initializing Services

To set a service, call the **setService** method. **setService** requires two parameters:

- service - (string) the name of the service
- obj - (object) an object with methods that you want to call from other Jet views

For example, let's create a *masterTree* view and let other views get the ID of a selected item in the master tree.

```js
// views/tree.js
import {JetView} from "webix-jet";

export default class treeView extends JetView{
    config(){
        return { view:"tree" };
    }
    init() {
        this.app.setService("masterTree", {
            getSelected : () => this.getRoot().getSelectedId();
        })
    }
}
```

*getSelected* of the *masterTree* service calls the **getSelectedId** method of Webix Tree view. **getSelectedId** returns the ID of the selected node of the tree.

## Using Services

To get to the service and its methods, use the **app.getService** method. **getService** requires one parameter - the name of the service.

This is how you can get the ID of the selected node and use it to fill a form with correct values:

```js
// views/form.js
import {JetView} from "webix-jet";
import {getData} from "../models/records";

export default class FormView extends JetView{
    config(){
        return {
            view:"form", elements:[
                { view:"text", name:"name" },
                { view:"text", name:"email" }
            ]
        };
    }
    init(){
        var id = this.app.getService("masterTree").getSelected();
        this.getRoot().setValues( getData(id) );
    }
}
```

Apart from view communication, services can be used for loading and saving data. [You can read about it in the "Models" chapter](models.md#services).

### Which Way of View Communication To Choose?

|            | Used For                     | Direction     | Receivers |
|------------|------------------------------|---------------|-----------|
| Parameters | Initialization, Data loading | Up->Bottom    | One       |
| Methods    | Actions                      | Child->Parent | Children  |
| Events     | Actions                      | Bottom->Up    | Many      |
| Services   | State Requests               | Any           | Many      |

Also remember that you **can't use the same service for two instances of a view class**, e.g. if you create a file manager with two identical file views. A new service is created for each instance of the class. Use services if other ways aren't possible.