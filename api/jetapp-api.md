# JetApp API

Here you can find the list of all the **JetApp** methods, events, and properties.

## Methods

| Method | Use |
| :--- | :--- |
| [attachEvent](jetapp-methods.md#app-attachevent) | attaches an event |
| [callEvent](jetapp-methods.md#app-callevent) | calls an event |
| [contains](jetapp-methods.md#app-contains) | checks if the app contains SomeView |
| [createView](jetapp-methods.md#app-createview) | creates a new Jet view |
| [getService](jetapp-methods.md#app-getservice) | returns a service |
| [getSubView](jetapp-methods.md#app-getsubview) | returns the current top view of the app |
| [getUrl](jetapp-methods.md#app-geturl) | returns an arrays of the URL segments |
| [refresh](jetapp-methods.md#app-refresh) | repaints the UI of an app |
| [render](jetapp-methods.md#app-render) | renders the app or the app module |
| [setService](jetapp-methods.md#app-setservice) | sets a service |
| [show](jetapp-methods.md#app-show) | rebuilds the app or app module according to the new URL |
| [use](jetapp-methods.md#app-use) | enables a plugin |

## Events

| Event | Use |
| :--- | :--- |
| [app:render](jetapp-events.md#app-render) | before each view of an app is rendered |
| [app:route](jetapp-events.md#app-route) | after navigation to a view |
| [app:guard](jetapp-events.md#app-guard) | before navigation to another view |
| [app:user:login](jetapp-events.md#app-user-login) | by the _User_ plugin when a user logs in |
| [app:user:logout](jetapp-events.md#app-user-logout) | by the _User_ plugin when a user logs out |
| [app:error](jetapp-events.md#app-error) | any error occurs |
| [app:error:resolve](jetapp-events.md#app-error-resolve) | when Jet can't find a module by its name |
| [app:error:render](jetapp-events.md#app-error-render) | on errors during view rendering, mostly Webix UI related |
| [app:error:initview](jetapp-events.md#app-error-initview) | in the case of an error during view rendering, mostly Webix Jet related |

## Properties

| Property | Use |
| :--- | :--- |
| [config](jetapp-properties.md#config) | app configuration settings |
| [ready](jetapp-properties.md#ready) | promise that resolves when the app is rendered |


