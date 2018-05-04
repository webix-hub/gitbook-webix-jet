## Version 1.5 - April 24, 2018

- the ability to compile a Jet app into a standalone bundle and [use it in other apps or as is](practice/deploy.md)
- the ability to compile a Jet app as a component and [use it as a Webix widget](practice/deploy.md)
- [**this.$$()**](details/views.md#this-usdusd) returns [widgets from the current Jet view only](details/referencing.md#5-referencing-webix-widgets)

## Version 1.4 - April 11, 2018

- JetView class instances can be created [in **config()**](details/subview.md#jetview-constructor)

## Version 1.3 - January 11, 2018

- [TypeScript support](practice/typescript.md)
- new API:
    - [getParam](details/views.md#getparam) and [setParam](details/views.md#this-setparam) methods to get/set URL parameters
    - [getUrl](details/views.md)
- [*UrlParam*](details/plugins.md#urlparam-plugin) plugin
- ability to set optional path for the [Locale plugin](details/plugins.md#locale-plugin)
- ["app:user:login"](details/inner_events.md#app-user-login) and ["app:user:logout"](details/inner_events.md#app-user-logout) events

## Version 1.2 - December 12, 2017

- [*show*](details/views.md#this-show) and [*getSubView*](details/views.md#this-getsubview) APIs updated
- *this.ui* updated
    - allows to define [custom HTML parent](details/views.md#optional-container-parameter)
    - can be used with [JetView classes](details/popups.md#windows-as-jet-view-classes)
- ability to [remove *"!"* from the hash URL](details/routers.md#hiding-the-in-the-url)
- ability to [return a promise from the *config* method of a view](details/async.md#a-promise-returned-by-config-of-a-class-view)
- HashRouter supports [*config.routes*](details/app_config.md#beautifying-the-url)
- views can define [string-to-string mapping](details/app_config.md#changing-view-creation-logic)

## Webix Jet 1.0 - September 26, 2017

Major update after version 0.5:
- [views](basic/views.md) and [app modules](basic/app.md) as ES6 classes
- using [Webpack](practice/webpackconfig.md) as a module bundler
- ability to choose among [4 routers](details/routers.md)
- ability to use [6 plugins](details/plugins.md) for common tasks
