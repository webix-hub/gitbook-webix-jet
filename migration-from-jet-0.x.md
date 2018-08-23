# Migration from Jet 0.x

## Toolchain and App

* Setup [wjet](https://webix.gitbook.io/webix-jet/part-iii-practical-tasks/wjet-utility-for-faster-prototyping) tool from the link.
* Create new `webix-jet` app using:  
```text
mkdir new-app // this will be your new application folder when migration is finished.
cd new-app
wjet init
npm install
```
* (In your original app) create folder sources and move all dev files there \( _app.js_, _models/_, _views/_, _helpers/_, etc. \)
* Copy the `app.js` file from `new-app` you created above to the `sources` folder. If you have any custom code in your original `app.js` then copy that in newer `app.js` too.
* Copy over `sources` folder to the `new-app`.
* Make sure in `new-app`, you have a locales folder with you localization files. Like [this repo](https://github.com/webix-hub/jet-start/tree/master/sources/locales)
* run the app 

```text
cd new-app
npm start
```

* open app at _localhost:8080_

## Troubleshooting
* If you get `can not resolve` error in webpack make sure you add the alias and path to that lib in webpack.config.js. [docs](https://webpack.js.org/configuration/resolve/)
* If something else then use this [repo](https://github.com/webix-hub/jet-start) as base for migration and replace the sources folder as above.

## Migrating views

Jet 1.x can recognize old configuration objects and use them correctly, so you will need not to change anything in a common case. There are still some scenarios when you will need to change the code:

### View has "app" as dependency

Change object to JetView-based class and use `this.app` instead of `app`.

### View is using locale

Change object to JetView and use `this.getService('locale')` instead of `locale`.

