# Changing the Port of the Dev Server

You can change the default port 8080 to another port \(e.g. 3010\). You can do this by changing the [_package.json_](https://docs.npmjs.com/getting-started/using-a-package.json) file.

Modify the _"start"_ command in the _package.json_ file:

```javascript
// package.json
...
"scripts": { 
     ... 
     "start": "webpack-dev-server --port 3010"
}
```

