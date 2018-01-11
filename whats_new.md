<style>
.date{
    color:#999;
    font-size:80%;
    padding-left:5em;
}
</style>

# What's New

### Version 1.3.2 <span class="date">January 11, 2018</span>

- **update**:
    - ES6 and typescript versions merged

### Version 1.3.0 <span class="date">January 10, 2018</span>

[update] new api renaming, show supports hash of parameters
[dev] code tests and lint
[fix] correct patch for older versions of webix UI

### <span class="date">January 9, 2018</span>

[update] url vars, ts-loader

### Version 1.2.1 <span class="date">December 12, 2017</span>

- **fix**:
    - regression in *this.$$*

### Version 1.2.0 <span class="date">December 12, 2017</span>

- **new**:
    - ES5 toolchain

- **updates**:
    - more visible error messages
    - *show* and *getSubView* APIs
    - encoding URL parameters

- **fix**:
    - optional config parameter for *this.ui*

### Version 1.1.0 <span class="date">December 6, 2017</span>

- **new**:
    - ability to remove *"!"* from hash URL
    - ability to return promise from the *config* method of a view

### Version 1.0.10 <span class="date">November 23, 2017</span>

- **updates**:
    - HashRouters supports *config.routes*
    - views can define string-to-string mapping

- **fix**:
    - the *get* method of Hash router returns non-active value

### Version 1.0.9 <span class="date">November 9, 2017</span>

- **fix**:
    - hash router is incompatible with some webpack-babel configs

### Version 1.0.8 <span class="date">October 27, 2017</span>

- **fix**:
    - regressions in App-Views

### Version 1.0.7 <span class="date">October 27, 2017</span>

- **update**:
    - *app:render* event provides replaceable object as the third argument
- **fix**:
    - correct error handling for rendering errors

### Version 1.0.6 <span class="date">October 20, 2017</span>

- **new**:
    - code linting
    - *view.ui* can be used to initialize JetView
- **update**:
    - better error handling, freeze patch
- **fix**:
    - app:guard handling

### Version 1.0.5 <span class="date">October 6, 2017</span>

- **fixes**:
    - better support of old Webix Jet code
    - Url router can't use prefix

### Version 1.0.3 <span class="date">October 5, 2017</span>

- **new**:
    - ES6 watch-rebuild task
- **updates**:
    - regression in Theme plugin
    - regressions in Locale and Theme plugins
- **fixes**:
    - regression in Url router
    - regressions in the User and Status plugins

### Webix Jet 1.0 <span class="date">September 26, 2017</span>