## Toolchain and App

- You need to add _webpack.config.js_ and package.json similar to https://github.com/webix-hub/jet-start 

If you already have _package.json_ in your project, just add the missed dependencies.

```
npm install --save-dev wjet babel-core babel-loader babel-preset-env css-loader file-loader less less-loader url-loader webpack webpack-dev-server extract-text-webpack-plugin
npm install webix-jet

//or

yarn add -D wjet babel-core babel-loader babel-preset-env css-loader file-loader less less-loader url-loader webpack webpack-dev-server extract-text-webpack-plugin
yarn add webix-jet
```

- create folder sources and move all dev files there ( app.js, models/, views/, helpers/, etc. )
- in webpack.config.json change the entry field to the main file of your app (entry: "sources/app.js")
- update app.js similar to https://github.com/webix-hub/jet-start/blob/master/sources/myapp.js
    - import JetApp
    - replace "core.create" with "new JetApp"
    - remove "view.use" commands if any
    - add app.render() call
- run the app 

```
npm start
```
- open app at localhost:8080

## Migrating views

Jet 1.x can recognize old configuration objects and use them correctly, so you will need not to change anything in a common case. There are still some scenarios when you will need to change the code:

### View has "app" as dependency

Change object to JetView-based class and use "this.app" instead of "app"

### View is using locale

Change object to JetView and use "this.getService('locale')" instead of 'locale'