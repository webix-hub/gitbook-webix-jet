# JetView API

## Methods

The JetView class has the following methods:

| Method | Use |
| :--- | :--- |
| [contains()](jetview-methods.md#this-contains) | checks if a view contains a subview |
| [getParam\(\)](jetview-methods.md#this-getparam) | returns a URL parameter |
| [getParentView\(\)](jetview-methods.md#this-getparentview) | returns the parent view |
| [getRoot\(\)](jetview-methods.md#this-getroot) | returns the top Webix widget in the view |
| [getSubView\(\)](jetview-methods.md#this-getsubview) | returns a subview of the view |
| [getSubViewInfo\(\)](jetview-methods.md#this-getsubviewinfo) | returns the object with the info on the subview and a pointer to its parent |
| [getUrl\(\)](jetview-methods.md#this-geturl) | returns an array of the URL segments related to a view |
| [getUrlString()](jetview-methods.md#this-geturlstring) | returns the URL as a string	|
| [on\(\)](jetview-methods.md#this-on) | attaches an event listener |
| [refresh\(\)](jetview-methods.md#this-refresh) | repaints the view and its subviews |
| [setParam\(\)](jetview-methods.md#this-setparam) | sets the URL parameters |
| [show\(\)](jetview-methods.md#this-show) | shows a subview |
| [ui\(\)](jetview-methods.md#this-ui) | creates a popup or a window |
| [use\(\)](jetview-methods.md#this-use) | enables a plugin |
| [$$\(\)](jetview-methods.md#this-usdusd) | returns a Webix widget by its ID |

JetView also has life-time handlers that are called automatically:

| Method | Use |
| :--- | :--- |
| [config()](jetview-handlers.md#config) | is called when the UI of a view is created |
| [init()](jetview-handlers.md#init) | is called when a view is initialized |
| [urlChange()](jetview-handlers.md#urlchange) | is called when the app URL is changing for this view |
| [ready()](jetview-handlers.md#ready) | is called when the view and all its inner views are fully initialized |
| [destroy()](jetview-handlers.md#destroy) | is called when a view is destroyed |

## Properties

| Property | Use |
| :--- | :-- |
| [app](jetview-properties.md#app) | refers to the app |

