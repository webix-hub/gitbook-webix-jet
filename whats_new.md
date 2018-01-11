<style>
.date{
    color:#999;
    font-size:80%;
    padding-left:5em;
}
</style>

# What's New

### Version 1.3 <span class="date">January 11, 2018</span>

- TypeScript support
- new API:
    - [getParam and setParam](details/views.md) methods to get/set URL parameters
    - [getUrl](details/views.md)
- [*UrlParam* plugin](details/plugins.md#urlparam)

### Version 1.2 <span class="date">December 12, 2017</span>

- [*show*](details/views.md#show) and [*getSubView*](details/views.md#getsub) APIs updated
- *this.ui* updated
    - [allows to define custom HTML parent](details/views.md#container)
    - [can be used with JetView classes](details/popups.md#class)

- ability to [remove *"!"* from the hash URL](details/routers.md#hash)
- ability to return a promise from the *config* method of a view
- HashRouter supports [*config.routes*](details/app_config.md#routes)
- views can define [string-to-string mapping](details/app_config.md#views)

### Webix Jet 1.0 <span class="date">September 26, 2017</span>

Major update after version 0.5:
- [views](basic/views.md) and [app modules](basic/app.md) as ES6 classes
- [using Webpack as a module bundler](practice/webpackconfig.md)
- [ability to choose among 4 routers](details/routers.md)
- [6 plugins](details/plugins.md)