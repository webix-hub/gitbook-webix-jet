[*package.json*](https://docs.npmjs.com/getting-started/using-a-package.json) is needed to manage locally installed npm packages.

You can reconfigure your app by changing this file.

## Changing the Port

You can change the default port 8080 to another port (e.g. 3010).

Modify the _"start"_ command in the *package.json* file:

```json
// package.json
...
"scripts": { 
     ... 
     "start": "webpack-dev-server --port 3010"
}
```
