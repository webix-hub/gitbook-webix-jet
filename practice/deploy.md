<style>
.code{
    font-family: Consolas, monospace;
}
</style>

# <span id="contents">Testing and Deployment</span>

- [Deploying the App](#production)
- [Testing the App](#testing)

## [<span id="production">Deploying the App &uarr;</span>](#contents)

When your app is complete, you need to build it for production.

If you are using *webpack.config.js* from the **jet-start** project, you can run either of these commands:

```bash
npm run build
```

or

```bash
webpack --env.production true
``` 

Any of this commands will compile the whole app in two files, **myapp.css** and **myapp.js**, which will be stored in the **codebase** folder (names may differ, based on the webpack config).

Now, you need to upload the files from **codebase** and **index.html** (the html page which is stored at the root of the project) to the production server.

By default, **index.html** uses the CDN version of Webix. If you are using Webix PRO, you need to change paths in **index.html** to the place where *webix.js* and *webix.css* are stored on the production server. 

After building an app, you need to include **webix.js** and **codebase/myapp.js**. There are no extra dependencies.

## [<span id="testing">Testing the App &uarr;</span>](#contents)

To check the quality of the source code, run the following command:

```bash
npm run lint
```
