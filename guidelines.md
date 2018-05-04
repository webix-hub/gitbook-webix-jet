## Debugging

These few issues will make your life a lot easier, so during development, remember to: 

- include [webix_debug.js](https://blog.webix.com/ui-development-and-debug-with-webix-js/),
- include [debug:true](details/app_config.md#debugging) into the app config,
- handle the ["app:error:resolve"](details/inner_events.md#app-error-resolve) event,
- check the quality of your code before each commit (```npm run lint```).

## Jet-style Development

And remember this important advice:

- [reuse similar views](details/subviews.md#3-class-views)
- [don't address views from other modules by IDs](details/events.md)
- [ensure that data is loaded before working with it](details/async.md)