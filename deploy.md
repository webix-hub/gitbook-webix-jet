# Deploying App

When your app is complete and you are ready let it out in the world, you need to build it for production.

If you are using *webpack.config.js* from the **jet-start** project, you can run:
`webpack --env.production true` 
or 
`npm run build`

Any of this commands will compile the whole app in two files, **myapp.css** and **myapp.js**, which will be stored in the **codebase** folder (names may differ, based on the webpack config).

Now, you need to upload the files from **codebase** and **index.html** (the html page which is stored at the root of the project) and that is all.

By default, **index.html** uses the CDN version of Webix. If you are using Webix PRO, you need to fix paths in **index.html** to the place where *webix.js* and *webix.css* are stored on the production server. 

After building an app, you need to include **webix.js** and **codebase/myapp.js**. There are no any extra dependencies.