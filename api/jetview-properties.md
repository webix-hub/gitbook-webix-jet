# JetView Properties

## app

Refers to the app in which the current view is initialized. **app** can be used for accessing app config and methods.

```javascript
// views/top.js
import {JetView} from "webix-jet";
export default class TopView extends JetView {
    config(){
        const _ = this.app.getService("locale")._;
        return {
            template: _("Welcome to the world of eternal happiness!")
        };
    }
}
```
