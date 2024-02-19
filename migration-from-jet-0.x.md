# Migration from Jet 0.x

## Toolchain and App

* Setup the [wjet](https://webix.gitbook.io/webix-jet/part-iii-practical-tasks/wjet-utility-for-faster-prototyping) tool from the link.
* Create a new `webix-jet` app using:

```text
mkdir new-app // this will be your new application folder when migration is finished.
cd new-app
wjet init
npm install
```

* In your original app, create the **sources** folder and move all the dev files there \( _app.js_, _models/_, _views/_, _helpers/_, etc. \).
* Copy the `app.js` file from `new-app` you created above to the `sources` folder. If you have any custom code in your original `app.js` then copy that in the newer `app.js` too.
* Copy the `sources` folder to the `new-app`.
* Make sure that there is the **locales** folder with you localization files in `new-app`. [Check this repo for example](https://github.com/webix-hub/jet-start/tree/master/sources/locales).
* run the app.

```text
npm start
```

* open the app at _localhost:5173_.

## Troubleshooting

* If something goes wrong, use the [starter jet repo](https://github.com/webix-hub/jet-start) as the base for migration and replace the sources folder as above.

## Migrating views

Jet 1.x can recognize old configuration objects and use them correctly, so you will not need to change anything in a common case. There are still some scenarios when you will need to change the code:

### View has "app" as dependency

Change object to JetView-based class and use `this.app` instead of `app`.

### View is using locale

Change object to JetView and use `this.getService('locale')` instead of `locale`.

