# WJet Utility for Faster Prototyping

[WJet](https://github.com/webix-hub/wjet) is a Webix Jet command line tool that will help you begin working with Webix Jet. 

## How to install

Clone or download the files and run:

```npm install -g wjet``` or ```yarn global add wjet```

## How to use

To create a skeleton app, run the following commands:

```
mkdir myapp
cd myapp
wjet init
```

You will be offered to create a name for the app, choose ES6 or TypeScript, add CSS-preprocessing tools, set the skin and the version of Webix (GPL or PRO).

After running the tool, you still need to install dependencies

```
npm install
```

and run the app

```
npm run start
```

## How to Add Localization and Authorization

To add localization or authorization, run the following command:

```
wjet add feature
```

If you choose ```Localization```, WJet will add the page for settings, a switch for locales, and the Locale plugin

If you choose ```Authentication```, WJet will add the User plugin and the client side for logging in. Afterwards you will need to provide a real session model.

## Adding Webix Components

To add a Webix component (from [https://github.com/webix-hub/components](https://github.com/webix-hub/components)), run the following command:

```
wjet add widget
```

After that you will choose the necessary component (scheduler, gantt, etc) and will be offered to create a view for the component.

## WJet and Windows

DO NOT USE GITBASH!!!

Use ```cmd``` or ```powershell``` instead.

<!-- 

## Adding a View

To create a view, run the following command:

```
wjet add view
```

You will be offered several types of views:

- CRUD
- Tree on the left
- Navigation on top

Each newly added view will be added into the menu.

## Adding a Model

To add a model, run the following command:

```
wjet add model
```

You will be offered to choose the type of a model:

- static
- dynamic
 
After the model is created, you will be asked if you want to have a view for the model.
 -->
