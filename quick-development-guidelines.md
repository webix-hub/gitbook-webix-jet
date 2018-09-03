# Quick Development Guidelines

## Debugging

These few issues will make your life a lot easier, so during development, remember to:

* include [webix\_debug.js](https://blog.webix.com/ui-development-and-debug-with-webix-js/),
* set [`debug:true` in the app configuration](part-ii-webix-jet-in-details/app-config.md#debugging),
* handle the ["app:error:resolve"](part-ii-webix-jet-in-details/inner-events-and-error-handling.md#app-error-resolve) event,
* check the quality of your code before each commit \(`npm run lint`\).

## Jet-style Development

And remember this important advice:

* [reuse similar views](part-ii-webix-jet-in-details/views-and-subviews.md#3-class-views)
* [don't address views from other modules by IDs](part-ii-webix-jet-in-details/view-communication.md)
* [ensure that data is loaded before working with it](part-ii-webix-jet-in-details/asynchronous-views.md)

