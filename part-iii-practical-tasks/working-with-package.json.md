# Working with package.json

[_package.json_](https://docs.npmjs.com/getting-started/using-a-package.json) is needed to manage locally installed npm packages.

You can reconfigure your app by changing this file.

## Changing the Port

You can change the default port 8080 to another port \(e.g. 3010\).

Modify the _"start"_ command in the _package.json_ file:

```javascript
// package.json
...
"scripts": { 
     ... 
     "start": "webpack-dev-server --port 3010"
}
```

