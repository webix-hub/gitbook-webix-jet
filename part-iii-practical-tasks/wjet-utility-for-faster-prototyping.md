# WJet Utility for Faster Prototyping

[WJet](https://github.com/webix-hub/wjet) is a Webix Jet command line tool that will help you begin working with Webix Jet.

## How to install

Clone or download the files and run:

`npm install -g wjet` or `yarn global add wjet`

## How to use

To create a skeleton app, run the following commands:

```text
mkdir myapp
cd myapp
wjet init
```

You will be offered to create a name for the app, choose ES6 or TypeScript, add CSS-preprocessing tools, set the skin and the version of Webix \(GPL or PRO\).

After running the tool, you still need to install dependencies

```text
npm install
```

and run the app

```text
npm run start
```

## How to Add Localization and Authorization

To add localization or authorization, run the following command:

```text
wjet add feature
```

If you choose `Localization`, WJet will add the page for settings, a switch for locales, and the Locale plugin

If you choose `Authentication`, WJet will add the User plugin and the client side for logging in. Afterwards you will need to provide a real session model.

## Adding Webix Components

To add a Webix component \(from [https://github.com/webix-hub/components](https://github.com/webix-hub/components)\), run the following command:

```text
wjet add widget
```

After that you will choose the necessary component \(scheduler, gantt, etc\) and will be offered to create a view for the component.

## WJet and Windows

{% hint style="danger" %}
DO NOT USE GITBASH!!!
{% endhint %}

Use `cmd` or `powershell` instead.

