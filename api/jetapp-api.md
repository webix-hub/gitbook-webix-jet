# JetApp API

Here you can find the list of all the **JetApp** methods, events, and properties.

## Methods

| Method | Use |
| :--- | :--- |
| [attachEvent](api/jetapp-methods.md#app-attachevent) | attaches an event |
| [callEvent](api/jetapp-methods.md#app-callevent) | calls an event |
| [contains](api/jetapp-methods.md#app-contains) | checks if the app contains SomeView |
| [createView](api/jetapp-methods.md#app-createview) | creates a new Jet view |
| [getService](api/jetapp-methods.md#app-getservice) | returns a service |
| [getSubView](api/jetapp-methods.md#app-getsubview) | returns the current top view of the app |
| [getUrl](api/jetapp-methods.md#app-geturl) | returns an arrays of the URL segments |
| [refresh](api/jetapp-methods.md#app-refresh) | repaints the UI of an app |
| [render](api/jetapp-methods.md#app-render) | renders the app or the app module |
| [setService](api/jetapp-methods.md#app-setservice) | sets a service |
| [show](api/jetapp-methods.md#app-show) | rebuilds the app or app module according to the new URL |
| [use](api/jetapp-methods.md#app-use) | enables a plugin |

## Events

| Event | Use |
| :--- | :--- |
| [app:render](api/jetapp-events.md#app-render) | before each view of an app is rendered |
| [app:route](api/jetapp-events.md#app-route) | after navigation to a view |
| [app:guard](api/jetapp-events.md#app-guard) | before navigation to another view |
| [app:user:login](api/jetapp-events.md#app-user-login) | by the _User_ plugin when a user logs in |
| [app:user:logout](api/jetapp-events.md#app-user-logout) | by the _User_ plugin when a user logs out |
| [app:error](api/jetapp-events.md#app-error) | any error occurs |
| [app:error:resolve](api/jetapp-events.md#app-error-resolve) | when Jet can't find a module by its name |
| [app:error:render](api/jetapp-events.md#app-error-render) | on errors during view rendering, mostly Webix UI related |
| [app:error:initview](api/jetapp-events.md#app-error-initview) | in the case of an error during view rendering, mostly Webix Jet related |

## Properties

| Property | Use |
| :--- | :--- |
| [config](api/jetapp-properties.md#config) | app configuration settings |
| [ready](api/jetapp-properties.md#ready) | promise that resolves when the app is rendered |


