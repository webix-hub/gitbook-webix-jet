# Using TypeScript

Typescript is supported by Webix Jet since version 1.3. Benefits of combining Webix Jet and TypeScript:

* a smart autocomplete for Webix methods,
* hints on the use of methods,
* code validation.

You can find the demo of using TypeScript in the [**typescript** branch of the Jet starter pack](https://github.com/webix-hub/jet-start/tree/typescript).

## Configuring the Toolchain

### Adding _tsconfig.json_

**tsconfig.json** file must specify the files of your project and compiler options. Create the file and add paths to _models_ and _views_ to the object with _compiler options_:

```javascript
// tsconfig.json
{
    "compilerOptions": {
        ...
        "paths": {
            "models/*":["models/views/*"],
            "views/*":["sources/views/*"]
        }
    },
    ...
}
```

Typing definitions for Webix widgets, mixin interfaces and parameters for methods are stored in the **webix.d.ts** file. Add it to **files** in **tsconfig.json**:

```javascript
// tsconfig.json
{
    ...
    "files":[
        "node_modules/webix/webix.d.ts"
    ]
}
```

Also include your source **.ts** files:

```javascript
// tsconfig.json
{
    ...
    "include":[
        "sources/**/*.ts"
    ]
}
```

### Configuring Webpack

Go to **webpack.config.js** and change the entry point and other **.js** files to **.ts**, e.g.:

```javascript
// webpack.config.js
const config = {
    entry: "./sources/myapp.ts",
    ...
    resolve: {
        extensions: [".ts", ".js"],
        ...
    }
}
```

Use a different transpiler: [**ts-loader**](https://github.com/TypeStrong/ts-loader) instead of **babel-loader**:

```javascript
// webpack.config.js
const config = {
    ...
    module: {
        rules: [
            {
                test: /\.ts$/,
                loader: "ts-loader"
            },
            ...
        ]
    }
}
```

### Updating Dependencies

Don’t forget to update dependencies in **package.json**:

```javascript
// package.json
"devDependencies": {
    ...
    "ts-loader": "^3.2.0",
    "tslint": "^5.9.1",
    "typescript": "^2.6.2",
    ...
},
"dependencies": {
    "webix-jet": "^1.3.7"
}
```

## Changes in the Codebase

All view and model files now must have the **.ts** extension. There is no need to change anything in files, as **webix-jet** is imported in the usual way and typings are enabled automatically.

If you want to use the APPNAME and VERSION defined in _package.json_, declare constants injected by webpack in the **app.ts** file:

```javascript
// sources/app.ts
import {JetApp} from "webix-jet";
import "./styles/app.css";

declare var APPNAME;
declare var VERSION;

webix.ready(() => {
    const app = new JetApp({
        id: APPNAME,
        version: VERSION,
        start: "/top/start"
    });
    app.render();
});
```

## Codebase Validation

Typescript toolchain includes the **tslint** tool, which can be used to validate your code and ensure its quality. To enable it, add **tslint.json** with [validation rules for TypeScript](https://github.com/webix-hub/jet-start/blob/typescript/tslint.json) into your project.

After that, include a script for linting into **package.json**:

```javascript
"scripts": {
    "lint": "tslint -p tsconfig.json",
    ...
}
```

Now you can run _npm run lint_ to validate the codebase of your app.

