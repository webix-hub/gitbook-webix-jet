# What's New

## Version 3.0 - May 25, 2023

* Toolchain migrated to Vite
* *onInit* event triggers during the init of jet views
* Predefined *_hidden* view
* Views can be overridden at runtime
* App can have a custom view loader
* Objects can be used as view params
* Ability to define app and view params through **config()** or **show()** commands
* Jetapp view doesn't copy methods of the app on self
* Popup subviews leave empty elements in parent collections

## Versions 2.1.1 - 2.1.7

* The ability to use a map of overrides in the view config
* Improved TypeScript support (locate views through generics)
* Correct handling of top-level JetView submodules on navigation
* Fixes to routers

## Version 2.1 - July 8, 2019

* Better routing logic (including subviews)
* *webix.fullscreen* support for JetView elements
* Typed results for navigation rejections (NavigationBlocked error)
* UnloadGuard: optimizations to guard triggers
* Various other fixes

## Version 2.0 - February 21, 2019

### New Features

* The ability to [show views in new windows](api/jetview-api.md#showing-a-subview-in-a-new-window)
* The ability to show windows like other views by [including them in the app URL](part-ii-webix-jet-in-details/popups-and-windows.md#including-windows-in-the-app-url)
* Several same-level subviews can have their [own app URLs](part-ii-webix-jet-in-details/views.md#3-several-dynamic-subviews) and work independently of each other
* *Menu plugin* can change [URL parameters](part-ii-webix-jet-in-details/plugins.md#using-the-plugin-to-change-url-parameters)
* *User plugin*: the ability to [add several pages accessible for non-authorized users](part-ii-webix-jet-in-details/plugins.md#adding-public-pages)
* *Locale plugin*: additional configuration property for [setting Webix locales](part-ii-webix-jet-in-details/plugins.md#combining-with-webix-locales)
* *Locale plugin*: the ability to [split localization and load parts on demand](part-ii-webix-jet-in-details/plugins.md#splitting-localization)
* *Locale plugin*: the ability to [configure Polyglot](part-ii-webix-jet-in-details/plugins.md#configuring-polyglot)
* *Locale plugin*: the ability to [disable locale loading from jet-locales](part-ii-webix-jet-in-details/plugins.md#path-for-the-locale-plugin)
* [*UrlRouter*](part-ii-webix-jet-in-details/routers.md#2-url-router) shares the same settings with [*HashRouter*](part-ii-webix-jet-in-details/routers.md#1-hash-router-default)
* Webix Jet supports IE11+
* *HashRouter* works more stable during in-browser navigation
* New [*SubRouter*](part-ii-webix-jet-in-details/routers.md#5-sub-router) that enables navigation in sub-modules
* The ability to use Webix Jet [without Webpack](part-iv-toolchain/using-webix-jet-without-webpack.md)
* The ability to [import Webix code as a module](part-iv-toolchain/importing-webix-as-module.md)

### Changes in API and Inner Logic

* [*getUrl()*](api/jetview-api.md#this-geturl) and [*getUrlString()*](api/jetview-api.md#this-geturlstring) for [app](api/jetapp-methods.md#app-geturl) and view
* *removeView()* of Webix widgets triggers *destroy()* of the Jet views inside them
* [*getSubView()*](api/jetapp-methods.md#app-getsubview) and [*contains()*](api/jetapp-methods.md#app-contains) can be called for JetApp
* The **refresh()** method for app and view returns a promise
* *view.refresh()* works for views with sub-elements
* *app.refresh()* works when an app is inside a Webix Jet view
* *app.refresh()* triggers refresh of the top view instead of using its own custom logic
* The order of destroy and init events: first, the old view is destroyed, then the new view is initialized

## Version 1.6 - June 26, 2018

* new [WJET utility](part-iv-toolchain/wjet-utility-for-faster-prototyping.md) for faster prototyping
* JetView has the [refresh\(\) method](api/jetview-api.md#this-refresh)
* better support for ES6 modules: [app.views](part-ii-webix-jet-in-details/app-config.md#code-splitting) can return a promise of an ES6 module, which can be used for code-splitting

## Version 1.5 - April 24, 2018

* the ability to compile a Jet app into a standalone bundle and [use it in other apps or as is](part-iv-toolchain/big-app-development.md#modules-and-large-app-development)
* the ability to compile a Jet app as a component and [use it as a Webix widget](part-iv-toolchain/big-app-development.md#using-jet-app-as-a-widget)
* [**this.$$\(\)**](api/jetview-api.md#this-usdusd) returns [widgets from the current Jet view only](part-ii-webix-jet-in-details/referencing-views.md#5-referencing-webix-widgets)

## Version 1.4 - April 11, 2018

* JetView class instances can be created [in **config\(\)**](part-ii-webix-jet-in-details/views.md#jetview-constructor)

## Version 1.3 - January 11, 2018

* [TypeScript support](part-iv-toolchain/using-typescript.md)
* new API:
  * [getParam](api/jetview-api.md#this-getparam) and [setParam](api/jetview-api.md#this-setparam) methods to get/set URL parameters
  * [getUrl](api/jetview-api.md#this-geturl)
* [_UrlParam_](part-ii-webix-jet-in-details/plugins.md#urlparam-plugin) plugin
* ability to set optional path for the [Locale plugin](part-ii-webix-jet-in-details/plugins.md#locale-plugin)
* ["app:user:login"](part-ii-webix-jet-in-details/api/jetapp-events.md#app-user-login) and ["app:user:logout"](part-ii-webix-jet-in-details/api/jetapp-events.md#app-user-logout) events

## Version 1.2 - December 12, 2017

* [_show_](api/jetview-api.md#this-show) and [_getSubView_](api/jetview-api.md#this-getsubview) APIs updated
* _this.ui_ updated
  * allows to define [custom HTML parent](api/jetview-api.md#optional-container-parameter)
  * can be used with [JetView classes](part-ii-webix-jet-in-details/popups-and-windows.md#windows-as-jet-view-classes)
* ability to [remove _"!"_ from the hash URL](part-ii-webix-jet-in-details/routers.md#hiding-the-in-the-url)
* ability to [return a promise from the _config_ method of a view](part-ii-webix-jet-in-details/asynchronous-views.md#a-promise-returned-by-config-of-a-class-view)
* HashRouter supports [_config.routes_](part-ii-webix-jet-in-details/app-config.md#beautifying-the-url)
* views can define [string-to-string mapping](part-ii-webix-jet-in-details/app-config.md#changing-view-creation-logic)

## Webix Jet 1.0 - September 26, 2017

Major update after version 0.5:

* [views](part-i-basic-usage/creating-views.md) and [app modules](part-i-basic-usage/creating-apps.md) as ES6 classes
* using [Webpack](https://webpack.js.org/) as a module bundler
* ability to choose among [4 routers](part-ii-webix-jet-in-details/routers.md)
* ability to use [6 plugins](part-ii-webix-jet-in-details/plugins.md) for common tasks

