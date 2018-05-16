# Extra Solutions

**Task:** you want to rebuild the existing view or replace some component in it

**Solution:** use **addView\(\)** of Webix views

**addView** is a Webix method of layout widgets like Form, MultiView, etc. You can use it to add Webix components to Jet views as well. Yet, it will work a little differently. The point is that **addView** uses regular webix.ui pattern of view initialization, which does not work with the Jet _$scope_. So, apart from the Webix view configuration that is usually passed to **addView** as a parameter, you must set **$scope** to **this**:

```javascript
someWebixView.addView({ $scope:this, view:"someWebixView" /* the rest of config */} [,position])
```

Have a look at the example. This is a Jet class view with a small Webix form:

```javascript
// views/start.js
import {JetView} from "webix-jet";

export default class StartView extends JetView {
    config(){
        return {
            view:"form", width:300, elements:[
                { view:"button", value:"Add a button" }
            ]
        };
    }
}
```

Let's dynamically add a button and define a class method **changeView** for that. To reference the Webix form from the method, use **this.getRoot\(\)**, where _this_ is the Jet class view and _getRoot_ returns the form.

```javascript
// views/start.js
import {JetView} from "webix-jet";

export default class StartView extends JetView {
    config(){
        return {
            view:"form", width:300, elements:[
                { view:"button", value:"Add a button", click: () => {
                    this.changeView();
                } }
            ]
        };
    }
    changeView(){
        this.getRoot().addView({
            $scope: this, 
            view:"button", 
            value:"Added" 
        });
    }
}
```

