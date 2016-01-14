---
layout: post
title: Making API without DB [Part1]
date: 2016-01-07
---

# In this post...
We're going to create API without database.
The goal of this post is to understand how to design API and how it works with express.
This post will be a bit long, so I decided to break it to three parts, and here is the part 1.
So, let's just get started!

# Making API

## Getting RESTful

### What is REST?
> REST stands for Representational State Transfer. It is a set of design principles for making network communication more scalable and flexible. These principles answer a number of questions. What are the components of the system? How should they communicate with each other? How do we ensure we can swap out different parts of the system at any time? How can the system be scaled up to serve billions of users?

source: [https://codewords.recurse.com/issues/five/what-restful-actually-means](https://codewords.recurse.com/issues/five/what-restful-actually-means)

>The modern web is mostly built around REST. the basics are that it should be stateless, use HTTP verbs explicitly, expose a directory like url pattern for routes, transfoer JSON and or XML.

source: [http://fem-node-api.netlify.com/](http://fem-node-api.netlify.com/)

## Determining resource

To make an API, we should determine what the actual resource looks like. We can model this in JSON.

```json
{
  "id": "1",
  "name": "Ryo",
  "age": "22",
  "gender": "male"
}
```

## Designing the routes

Next, we should design routes to access the resource.
We want to use the HTTP verbs (*GET*, *POST*, *PUT*, *DELETE*) to perform *CRUD* (*CREATE*, *READ*, *UPDATE*, *DELETE*)

The routes look like the structure below

```json
{
  "GET /users": {
    "desc": "returns all users",
    "response": "200 application/json",
    "data": [{}, {}, {}]
  },

  "GET /users/:id": {
    "desc": "returns one user respresented by its id",
    "response": "200 application/json",
    "data": {}
  },

  "POST /users": {
    "desc": "create and returns a new user uisng the posted object as the user",
    "response": "201 application/json",
    "data": {}
  },

  "PUT /users/:id": {
    "desc": "updates and returns the matching user with the posted update object",
    "response": "200 application/json",
    "data": {}
  },

  "DELETE /users/:id": {
    "desc": "deletes and returns the matching user",
    "response": "200 application/json",
    "data": {}
  }
}
```

Now we have the resource and routes modeled out, we can start building the this API with express.

# Middleware
Before we start writing code, there is one thing that we need to know.
That's middleware.

## What is middleware?

> Express is really just a routing and middleware framework. Middleware is a function with access to the request object, the response object, and the next() function that when called will go to the next middleware. Middleware can run any code, change the request and response objects, end the request-response cycle, and call the next middleware in the stack. If a middleware does not call next() or end the cycle, then the server will just hang.
There are 5 different types of middleware in Express 4.x.
- 3rd party
- Router level
- Application level
- Error-handling
- Built-in

source:
[http://fem-node-api.netlify.com/](http://fem-node-api.netlify.com/)

> Middleware functions can perform the following tasks:
- Execute any code.
- Make changes to the request and the response objects.
- End the request-response cycle.
- Call the next middleware function in the stack.

source:
[http://expressjs.com/en/guide/using-middleware.html](http://expressjs.com/en/guide/using-middleware.html)

As you can see the quotes above, middleware in express has accesses to request and response objects and next() function.
So middleware is gonna look like this.

```js
let middleware = (req, res, next) => {}
// or it looks like this
let middleware = () => {
  return (req, res, next) => {}
}
```

Error handling middleware which takes one extra argument as error

```js
let errorHandlingMiddleware = (err, req, res, next) => {}
```

## Next()
Every middleware has an argument called next.

> If the current middleware function does not end the request-response cycle, it must call next() to pass control to the next middleware function. Otherwise, the request will be left hanging.

## Setting middleware
First, you have to set global middleware using *app.use*
(*app.use* is setting global application middleware)

```js
...
// Set middleware
app.use(express.static('client'));
app.use(bodyParser.urlencoded({extended: true}));
app.use(bodyParser.json());
...
```

### body-parser
*body-parser* is a 3rd party lib.

Q. Why do we need body-parser?

A.
> When you submit form data with a POST request, that form data can be encoded in many ways. The default type for HTML forms is application/x-www-urlencoded, but you can also submit data as JSON or any other arbitrary format.
bodyParser.urlencoded() provides middleware for automatically parsing forms with the content-type application/x-www-urlencoded and storing the result as a dictionary (object) in req.body. The body-parser module also provides middleware for parsing JSON, plain text, or just returning a raw Buffer object for you to deal with as needed.

source: [https://www.reddit.com/r/learnprogramming/comments/2qr8om/nodejs_why_do_we_need_body_parser_urlencoded_in/](https://www.reddit.com/r/learnprogramming/comments/2qr8om/nodejs_why_do_we_need_body_parser_urlencoded_in/)

#### bodyParser.urlencoded(options)
> Returns middleware that only parses urlencoded bodies. This parser accepts only UTF-8 encoding of the body and supports automatic inflation of gzip and deflate encodings.
A new body object containing the parsed data is populated on the request object after the middleware (i.e. req.body). This object will contain key-value pairs, where the value can be a string or array (when extended is false), or any type (when extended is true).

source:
[https://github.com/expressjs/body-parser#bodyparserurlencodedoptions](https://github.com/expressjs/body-parser#bodyparserurlencodedoptions)

By the default option, extended is set to false which uses querystring lib, so if you want to use qs lib, just set it to true.

Q. What's the difference b/w qs and querystring?

A.
>Extended protocol uses qs library to parse x-www-form-urlencoded data. The main advantage of qs is that it uses very powerful serialization/deserialization algorithm, capable of serializing any json-like data structure.
But web-browsers don't normally use this protocol, because x-www-form-urlencoded was designed to serialize flat html forms. Though, it may come in handy if you're going to send rich data structures using ajax.
querystring library provides basic serialization/deserialization algorithm, the one used by all web-browsers to serialize form data. This basic algorithm is significantly simpler than extended one, but limited to flat data structures.
Both algorithms work exactly the same with flat data.

source:
[https://stackoverflow.com/questions/29175465/body-parser-extended-option-qs-vs-querystring](https://github.com/expressjs/body-parser#bodyparserurlencodedoptions)

#### bodyParser.json
> Returns middleware that only parses json. This parser accepts any Unicode encoding of the body and supports automatic inflation of gzip and deflate encodings.
A new body object containing the parsed data is populated on the request object after the middleware (i.e. req.body).

source:
[https://github.com/expressjs/body-parser#bodyparserjsonoptions](https://github.com/expressjs/body-parser#bodyparserjsonoptions)


<!-- ## File Structure
Before we write code, I want you to make sure how the file structure is gonna be. Below is the file structure that we gonna go through.

```
.
|-- server
   |-- api
      |-- category
         |-- categoryController.js
         |-- categoryModel.js
         |-- categoryRoutes.js
      |-- post
         |-- postController.js
         |-- postModel.js
         |-- postRoutes.js
      |-- user
         |-- userController.js
         |-- userModel.js
         |-- userRoutes.js
      |-- api.js
   |-- auth
      |-- auth.js
      |-- controller.js
      |-- routes.js
   |-- config
      |-- config.js
      |-- development.js
      |-- production.js
      |-- testing.js
   |-- middleware
      |-- appMiddleware.js
   |-- util
      |-- logger.js
      |-- seed.js
   |-- server.js
|-- index.js
|-- package.json
|-- procfile
|-- README.md
``` -->
