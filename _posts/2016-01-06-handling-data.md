---
layout: post
title: Handling data with Express
date: 2016-01-06
---

# In this post...
We'll create a server that handles data.

# Handling data
I'm sure that you already know how to setup and run the Node.js server with express. If you don't, please read [my previous post](./01-hello-world.md).

First, what I wanna do is that I'm gonna create a temporary JSON data and access to a certain URL to retrieve the JSON data.

So let's make it happen.

server.js

```js
  const express = require('express');
  const path = require('path');
  const app = express();

  const port = 3000;

  // Create a temporary JSON data contains any objects
  const jsonData = {
    name: 'Ryo Ikarashi',
    sex: 'male',
    age: 22
  };

  app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'), (err) => {
      if(err) {
        res.status(500).send(err);
      }
    });
  });

  // When accessed to /data, just return the JSON data.
  app.get('/data', (req, res) => {
    res.json(jsonData);
  });

  app.listen(port, () => {
    console.log('listening on http://localhost:', port);
  });
```

Let's see the result.
Go to [http://localhost:3000/data](http://localhost:3000/data), and you can see the JSON data returned by node server! yay!

Now you can know how to return the JSON data with express.
Next, we read a external data and return it back to browser.

First, we need to handle the external data, which means that we need a new npm module that is able to read external files.
So, we're gonna get a new npm module called [fs](https://www.npmjs.com/package/fs).

```bash
npm i fs --save
```

Then create a new file which contains JSON data.

```bash
touch data.json
```

data.json

```json
{
  "name": "Ryo Ikarashi",
  "sex": "male",
  "age": "22"
}
```

And modify the server.js

server.js

```js
  const express = require('express');
  const path = require('path');
  // we require a new module called "fs" to read external files.
  const fs = require('fs');
  const app = express();

  const port = 3000;

  app.get('/', (req, res) => {
    res.sendFile(path.join(__dirname, 'index.html'), (err) => {
      if(err) {
        res.status(500).send(err);
      }
    });
  });

  app.get('/data', (req, res) => {
    // using fs, read the data.json file synchronously and parser it JSON.
    const jsonData = JSON.parse(fs.readFileSync(path.join(__dirname, 'data.json'), 'utf-8'));
    // return the JSON data
    res.json(jsonData);
  });


  app.listen(port, () => {
    console.log('listening on http://localhost:', port);
  });
```

That's it! Very easy right?
Let's take a little bit deeper look at how fs.readFileSync and fs.readFile work.

While fs.readFileSync is reading a file synchronously, fs.readFile is reading a file asynchronously and it has a callback with a few arguments. The callback will be executed when a file is fully loaded, and you can access to the entire data referring to the arguments.

fs.readFileSync

```bash
node

> var data = fs.readFileSync('./data.json');

// data is a buffer
> data
> <Buffer 7b 0a 20 20 22 6e 61 6d 65 22 3a 20 22 52 79 6f 20 49 6b 61 72 61 73 68 69 22 2c 0a 20 20 22 73 65 78 22 3a 20 22 6d 61 6c 65 22 2c 0a 20 20 22 61 67 ... >

// and then parse it to JSON
var jsonData = JSON.parse(data);

// Now you can see the JSON object
> jsonData
{ name: 'Ryo Ikarashi', sex: 'male', age: '22' }
```

fs.readFile

```bash
node

> var bufferData;
> var jsonData;
> fs.readFile('./data.json', (err, buffer) => { bufferData = buffer; jsonData = JSON.parse(bufferData); });

> bufferData
> <Buffer 7b 0a 20 20 22 6e 61 6d 65 22 3a 20 22 52 79 6f 20 49 6b 61 72 61 73 68 69 22 2c 0a 20 20 22 73 65 78 22 3a 20 22 6d 61 6c 65 22 2c 0a 20 20 22 61 67 ... >

> jsonData
{ name: 'Ryo Ikarashi', sex: 'male', age: '22' }
```

Now you know the differences b/w readFileSync and readFile.
Notice that the data returned by fs.readFileSync is "Buffer", so you have to parse it to whatever data type you want.

# Next...
In the next post, we'll create a little bit bigger app using express.
