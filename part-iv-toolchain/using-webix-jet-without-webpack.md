# Using Webix Jet without Vite or Webpack

If you really want to, you can drop Vite or Webpack and include Jet source code directly to the page. ```npm``` contains two versions of Jet JS code:

- ES6 in *es6/jet.js* (used by default),
- ES5 in *es5/jet.js* (use this).

Once you include *es5/jet.js*, you will be able to use Jet like:

```javascript
class MyApp extends webix.jet.JetApp { /* ... */ }
```