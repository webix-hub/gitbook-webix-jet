# JetApp Events

## app:render

The event is triggered before each view of an app is rendered.

**Parameters:**

* **view** - the view for which the event is called
* **url** - an array of objects: the URL as an array of URL elements \(each element object has such properties as _page_, _params_, and _index_\)
* **result** - a wrapper object for UI; it's created in case you want to change the UI \(e.g. to put it into some other view\). **result** has only one property - **ui**.

You can use **app:render** to change the initial UI config and add properties to UI controls:

```javascript
// myapp.js
...
app.attachEvent("app:render", function(view,url,result){
    if (result.ui.view === "button")
        result.ui.disabled = true;
});
```

## app:route

Handling the **app:route** event resembles redefining the **urlChange\(\)** of a class view. The event fires after navigation to a view.

**Parameters:**

- **url** - the URL as an array of URL elements \(objects with three properties: _page_, _params_, and _index_\).

**app:route** can be used to send notifications or messages to a log:

```javascript
// myapp.js
...
app.attachEvent("app:route", function(url){
    webix.ajax("log.php", url);
});
```

**app:route** is used by the _Menu_ plugin to highlight menu options according to the URL.

## app:urlchange

The event fires when the app URL changes.

**Parameters:**

- **view** - the view for which the event is called
- **url** - the URL segment of the view

## app:guard

The **app:guard** event is triggered before navigation to another view.

**Parameters:**

* _url_ - a string with the attempted URL
* _view_ - the parent view that contains a subview that will be blocked
* _nav_ - an object that defines navigation to the next view

**nav** has three properties:

* _redirect_ is the new URL; in the example above, it's used for redirection
* _url_ is the URL split into an array of element objects \(each has three properties: _page_, _params_, and _index_\)
* _confirm_ is a promise that is resolved when the **app:guard** event is called

One of the typical cases to use **app:guard** is to create a guard: block some views and redirect users somewhere else.

```javascript
// myapp.js
...
app.attachEvent("app:guard", function(url, view, nav){
    if (url.indexOf("/blocked") !== -1){
        nav.redirect = "/top/allowed";
    }
});
```

[View demo on GitHub &gt;&gt;](https://github.com/webix-hub/jet-demos/blob/master/sources/appguard.js)

The **app:guard** event is used by the _UnloadGuard_ plugin.

## app:user:login

The **app:user:login** event is called by the User plugin when a user logs in.

**Parameters:**

- **user** - an object with user data.

```javascript
// myapp.js
...
app.attachEvent("app:user:login",function(user){
    webix.ajax("log.php", user);
})
```

## app:user:logout

The **app:user:logout** event is called by the User plugin when a user logs out.

```javascript
// myapp.js
...
app.attachEvent("app:user:logout",function(){
    app.show("loginform");  //load "views/loginform.js"
});
```

## app:error

This is a common event for all errors.

Parameters:

- **err** - the error object.

```javascript
// myapp.js
...
app.attachEvent("app:error", function(err){
    alert("Error");
});
```

## app:error:resolve

**app:error:resolve** fires when Jet can't find a module by its name.

Parameters:

* **err** - an error object
* **url** - the URL

If this error happens, it would be useful to redirect users somewhere else instead of showing them an empty screen:

```javascript
// myapp.js
...
app.attachEvent("app:error:resolve", function(err, url) {
    webix.delay(() => app.show("/some"));
});
```

## app:error:render

This event is triggered on errors during view rendering, mostly Webix UI related. It means that some view UI config has been written incorrectly.

**Parameters:**

- **err** - the error object.

```javascript
// myapp.js
...
app.attachEvent("app:error:render", function(err){
    alert("Check UI config");
});
```

## app:error:initview

This event is triggered in the case of an error during view rendering, mostly Webix Jet related. It means that Jet, while rendering Webix UIs, was unable to render the app UI correctly.

**Parameters:**

* **err** - the error object
* **view** - the view that caused the error

```javascript
// myapp.js
...
app.attachEvent("app:error:initview", function(err,view){
    alert("Unable to render");
});
```

## app:error:server

The event is called by the Status plugin when there is an error while setting a status.

**Parameters:**

- **err** - the error message or error object
