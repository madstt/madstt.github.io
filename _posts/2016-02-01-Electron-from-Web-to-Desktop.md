---
layout: post
title: Electron, From Web to Desktop - Part 1
date: 2016-01-22
categories:
- electron
published: true
---

Some time back I kicked the tires of [Electron](http://electron.atom.io). I really fancy the idea that you can use web technologies on your desktop. 

With this mindset I decided to try out Electron. In this first post in a series of Electron posts, I'll create a small and simple desktop app using Electron.

In this first post I'll be showing how quickly you can get up and running with Electron.

Once again, [npm](http://npm.org) is your friend. We create a folder in which we would like to hold our Electron desktop application:

```bash
> mkdir electron-hello
```
Then inside this folder we run `npm init`. You then have to answer some questions about application. This will create the `packages.json` file which contains some metadata about your application. This `packages.json` is mandatory in any Electron application.

Now we need to install the `electron-prebuilt` package:

```bash
> npm install electron-prebuilt --save-dev
```
This will install the `electron-prebuilt` package and store this dependecy as a development dependency in the `packages.json`, we created above.

Now go on and create a folder that will hold the application specific files:

```bash
> mkdir app
``` 
I've called the folder `app`. In this folder we once again run the `npm init` command, and answer the questions. Now we need to edit the `packages.json` file created. When you open the file in your favorite text editor it should look like this:
```json
{
  "name": "app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```
For my own convenience I change the `main` to `main.js`, so my `packages.json` will look like this:

```json
{
  "name": "app",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```
Now create the `main.js` in the `app` folder. In this very trivial sample application the `main.js` will look like this:

```javascript
var app = require('app');
var BrowserWindow = require('browser-window');
var mainWindow = null;

app.on('ready', function() {
   mainWindow = new BrowserWindow({
       width: 800,
       height: 600
   });
   
   mainWindow.loadUrl('file://' + __dirname + '/index.html');
   
   mainWindow.on('closed', function(){
       mainWindow = null;
   })
});
```
In the first line we require the `app`, which is our application. Next we require a `browser-window`. This is the actual Electron shell, which will host our application. The `mainWindow` variable will hold a reference to an instance of the `BrowserWindow`.

Then we hook up our application by listening to the `ready` event. So, when the application is ready we create the `BrowserWindow` instance and sets its dimensions. Then we load some content into the `mainWindow` by using the `loadUrl` method.

The `loadUrl` method can take anything basically. In this case we want to show a local `index.html`, but there is nothing that can stop you from host a website inside your Electron application. I could host and distribute this blog as a desktop application like this:

```javascript
mainWindow.loadUrl('http://www.madstt.dk');
``` 
The last line in my `main.js` is an event handler, which will release the reference to the `mainWindow` object upon the application is closed.

We're almost ready to take this little beast for a spin. We need to create `index.html`:

```html
<html>
    <head>
        <title>Hello from Electron!</title>
    </head>
    <body>
        <h1>Hello from Electron!</h1>
    </body>
</html>
```
This is very simple.

Now, to launch this application we move to the outer folder (the folder containing the app folder). Here we can run the following command:
```bash
> electron app
```
Now your first Electron application is airborne. That's was easy, right? In the following posts we'll extend this simple application with more advanced features. On the top of my mind, the next post will focus on setting up [gulp](http://gulpjs.com/) for building, running, packaging, and distributing the application. Other topics will be setting up AngularJS, crash reporting, distribution, automatic updating, debugging, etc.

Throw me a comment for suggestions. 