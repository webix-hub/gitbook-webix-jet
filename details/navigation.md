## Navigation with HTML links

Apart from navigating with links with the **route** attribute, you can create HTML links. This way is not so convenient, as the first one. The **route** attribute allows you to stop users from leaving the current view because of unsaved data, for instance. See the details in the [section on plugins](plugins.md). This is impossible with an HTML link.

In case there's no worries and you don't want this functionality, you can create links with the **href** attribute. Suppose you have these view modules:

~~~js
/* views/demo.js */
import {ToolbarView} from "toolbar"
const DemoView = {
    rows: [
        ToolbarView,
        { $subview: true }
    ]
};
/* views/details.js */
const DetailsView = {
    template: "App"
};
~~~

This is a link to the **Details** view as a subview of **DemoView**:

~~~html
<!--index.html-->
<a href="#!/Demo/Details">Data</a>
~~~