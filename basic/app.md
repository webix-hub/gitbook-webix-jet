## App

A Webix Jet app is single-page and is divided into views. The application structure consists of the following parts:

- the *index.html* file that is a start page of the app
- *app.js* that contains the configuration of the app
- the *views* folder that contains elements of the interface
- the *models* folder that contains data modules

App represents an application or an application module. It is used to group views and modules which implement some specific scenario. Later you will be able to combine separate app modules in high level apps.

### The Syntax of Creation

An app module is created as a new instanse of JetApp class. You are to pass an object with your app configuration like the app name, version, etc as the parameter.

~~~js
var app = new JetApp({
    ...//app configuration
}).render();
~~~

You can open the needed URL and the UI will be rendered from the URL elements. The app splits the URL into parts, finds the corresponding files in the **views** folder and creates an interface by combining UI modules from those files.