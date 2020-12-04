# \[DEPRECATED\] WJet Utility for Faster Prototyping

{% hint style="warning" %}
WJet is deprecated and is no longer maintained.
{% endhint %}


[WJet](https://github.com/webix-hub/wjet) is a Webix Jet command line tool that will help you begin working with Webix Jet.

## How to install

Clone or download the files and run:

`npm install -g wjet` or `yarn global add wjet`

## WJet and Windows

{% hint style="danger" %}
DO NOT USE GITBASH!!!
{% endhint %}

Use `cmd` or `powershell` instead.

## How to use

To create a skeleton app, run the following commands:

```bash
mkdir myapp
cd myapp
wjet init
```

You will be offered to create a name for the app, choose ES6 or TypeScript, add CSS-preprocessing tools, set the skin and the version of Webix \(GPL or PRO\).

After running the tool, you still need to install dependencies

```bash
npm install
```

and run the app

```bash
npm run start
```

## How to Add Localization and Authorization

To add localization or authorization, run the following command:

```bash
wjet add feature
```

If you choose `Localization`, WJet will add the page for settings, a switch for locales, and the Locale plugin

If you choose `Authentication`, WJet will add the User plugin and the client side for logging in. Afterwards you will need to provide a real session model.

## Adding Webix Components

To add a Webix component \(from [https://github.com/webix-hub/components](https://github.com/webix-hub/components)\), complex widget (e.g. Kanban or Spreadsheet) or a DHX widget (Scheduler, Gantt) run the following command:

```bash
wjet add widget
```

After that you will choose the component and will be offered to create a view for the component. Additionally, you will be able to create or choose a data model for the view.

## Adding Jet Views

To add a new view, run:

```bash
wjet add view
```

There are several presets for views:

- a view for navigation with subviews,
- a view based on a complex widget,
- a view for CRUD operations (a form or a datatable),
- a view for data navigation (a view with several data components and/or a form).

## Adding Models

To add a new data model, run:

```bash
wjet add model
```

You can add the following kinds of models:

- static (JS data),
- a data collection,
- a proxy for the server API.

