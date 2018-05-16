# Deploying and Testing

## Deploying the App

When your app is complete, you need to build it for production.

If you are using _webpack.config.js_ from the **jet-start** project, you can run either of these commands:

```bash
npm run build
```

or

```bash
webpack --env.production true
```

Any of this commands will compile the whole app in two files, **myapp.css** and **myapp.js**, which will be stored in the **codebase** folder \(names may differ, based on the webpack config\).

Now, you need to upload the files from **codebase** and **index.html** \(the html page which is stored at the root of the project\) to the production server.

By default, **index.html** uses the CDN version of Webix. If you are using Webix PRO, you need to change paths in **index.html** to the place where _webix.js_ and _webix.css_ are stored on the production server.

After building an app, you need to include **webix.js** and **codebase/myapp.js**. There are no extra dependencies.

## Testing the App

To check the quality of the source code, run the following command:

```bash
npm run lint
```

To test the source code, you can use the mocha test example from the [**tests** branch of _jet-start_](https://github.com/webix-hub/jet-start/tree/tests).

Once you have:

* added [setup.js](https://github.com/webix-hub/jet-start/commit/da672f703808dd62be7267d76ff9dc9bc6580805#diff-81e799b66ac20327dd8a1b8bb1f5f802) to prepare the JSDOM environment for testing,
* added [webpack.test.config.js](https://github.com/webix-hub/jet-start/commit/da672f703808dd62be7267d76ff9dc9bc6580805#diff-7e9207f7db6729521af653acff794836),
* made necessary changes to your [package.json](https://github.com/webix-hub/jet-start/commit/da672f703808dd62be7267d76ff9dc9bc6580805#diff-b9cfc7f2cdf78a7f4b91a753d10865a2) \(npm script for mocha and dev dependencies\),
* added [test files](https://github.com/webix-hub/jet-start/tree/tests/tests) for each view and model file in your project,

run the following command:

```bash
npm run test
```

After the tests are complete, you will see something like this:

![Webix Jet with webpack-mocha tests that ran successfully](../.gitbook/assets/mocha.png)

