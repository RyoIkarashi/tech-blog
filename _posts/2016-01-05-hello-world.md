---
layout: post
title: Hello world!
date: 2016-01-05
---

# Before getting started

Before getting started, there are a few things that you have to follow.

  1 -  Create a project folder

  ```bash
  mkdir ~/Sites/my-new-express-app && cd ~/Site/my-new-express-app
  ```

  2 - Initialize NPM

  ```bash
   npm init --yes
  ```

  3 - Install some NPM modules

  ```bash
  npm i express body-parser morgan path loadash --save
  ```

These are the modules that you need to install first
 - [express](https://www.npmjs.com/package/express)
 - [body-parser](https://www.npmjs.com/package/body-parser)

Once you've followed every steps, now you're ready to go!

# Hello world
Every time you learn a new programming language, many people may just  begin with "hello world" right? So, no reason not to begin with this!

  1 - Create a index.html file in the project root

  ```bash
    touch index.html
  ```

  Here is the index.html

  ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>My First Express App!</title>
    </head>
    <body>
      <h1>Hello World! :)</h1>
    </body>
    </html>
  ```

  2 - Then create a file called server.js in your project root

  ```bash
    touch server.js
  ```

  3 - Create a web server with Node.js using Express

  ```js
    // First of all, require all modules that you need to use
    const express = require('express');
    const path = require('path');

    // Create a express app
    const app = express();

    // The port number that you want the server to listen on
    const port = 3000;

    // when accessed to the root, then send the file, index.html to browsers
    app.get('/', (req, res) => {
      res.sendFile(path.join(__dirname, 'index.html'), (err) => {
        // If the file was not found, then throw an error
        if(err) {
          // add 500 HTTP Status and send an error
          res.status(500).send(err);
        }
      });
    });

    // express app listens on port 3000
    app.listen(port, () => {
      // when server started, output a message on a terminal
      console.log('listening on http://localhost:', port);
    });

  ```

  and start the server

  ```bash
    node server.js
  ```

  Your Node.js server will immediately start and listen on [http://localhost:3000](http://localhost:3000).
  So all you have to do is to open up a browser and go to [http://localhost:3000](http://localhost:3000).
  You'll see the page saying "Hello world! :)"

  Actually, that's it!
  Now you can see how easy to set up the server and run it.

# Next...
Now you've created a node.js server and ran it with no difficulty.
So in the next post, we will create an app which handles some external data.
