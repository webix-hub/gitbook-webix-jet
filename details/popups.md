# Working with Popup Elements

```js
/* views/toolbar.js */
export default class ToolbarView extends JetView{
    config(){
        return { 
            view:"toolbar", elements:[
                { view:"label", label:"Demo" },
                { view:"segmented", localId:"control", options:["Details", "Dash"], click:function(){
                    this.$scope.app.show("/Demo/"+this.getValue())
                }}
            ]
        };
    }
    init(view, url){
        var popup = webix.ui({
            view:"popup", 
            body:"Toolbar is created"
        });

        if (url.length)
            this.$$("control").setValue(url[1].page);
    }
    urlChange(view, url){
        if (url.length > 1)
            this.$$("control").setValue(url[1].page);
    }
    destroy(){
        popup.destructor();
    }
}
```