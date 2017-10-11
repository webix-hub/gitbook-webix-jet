## App API

Here you can find the list of all the **JetApp** methods, that you can make use of.

#### 1. app.render

The **render** method is the method that builds the UI of the application. If called without any parameters, it just renders the UI according to the start URL, specified in the app configuration. The app UI takes the whole page.

```js
app.render();
```

But if you want to render the app inside of a container, you can pass the string parameter to it with the ID of the container:

```js
app.render("mybox");
```

#### 2. app.show

The **show** method is used to change the interface. This method rebuilds the whole UI of the app according to the URL passed as a parameter:

```js
app.show("/demo/details")
```

For more info about showing UI components, [visit the section on view referencing](referencing.md).

#### 3. app.use

The **use** method is used to include plugins. The method takes two parameters:

- the name of the plugin 
- the plugin configuration

~~~js
this.use(JetApp.plugins.PluginName, {
    /* config */
});
~~~

For more details, [go to the dedicated section](plugins.md) of this chapter.


