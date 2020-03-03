# Quick Development Guidelines

## Debugging

These few issues will make your life a lot easier, so during development, remember to:

* use the [debug version of the source code](https://blog.webix.com/ui-development-and-debug-with-webix-js/),
* set [`debug:true` in the app configuration](part-ii-webix-jet-in-details/app-config.md#debugging),
* handle the ["app:error:resolve"](api/jetapp-events.md#app-error-resolve) event,
* check the quality of your code before each commit with \(`npm run lint`\).

## Jet-style Development

And remember this important advice:

* [reuse similar views](part-ii-webix-jet-in-details/views-and-subviews.md#3-class-views)
* [don't address views from other modules by IDs](part-ii-webix-jet-in-details/view-communication.md)
* [ensure that data is loaded before working with it](part-ii-webix-jet-in-details/asynchronous-views.md)
