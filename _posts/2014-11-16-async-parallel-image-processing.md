---
layout: post
title: "Async Parallel Image Processing"
description: "stream files to mongodb"
tagline: "serve side image processing"
category: [server-side]
tags: [mongodb, busboy, gridfs-stream, async, mongoose, nodejs, sharp, coffee-script]
---
{% include JB/setup %}

###Processing file updloads on the server.

NodeJS streams are incredibly powerful.  For a site I'm working on, users are allowed to upload images from their mobile devices. Typically these are a camera photo or an image file selected from the local gallery or perhaps from clould storage.  
 
The code below is an Express request handler written in CoffeeScript that handles a mult-part http POST containing a file.  It gets better. The process uses NodeJS streams, as opposed to writing temp files to the file-system, and it streams the file into a Mongdb grid-fs filestore.  Streams are great because not only does the file not touch a file system, they are processed in a non-blocking asyncronous fashion. Files never touch the file system, they pipe there way straight into a NoSQL datastore. I was gonna stop right there and call it a day, problem solved.  I achieved my goal of saving file uploads into a Mongo Gridstore, Bob's your Uncle! I can quit now.  But nooo, I'd been reading up on [Async JavaScript](https://pragprog.com/book/tbajs/async-javascript) and one day wanted to put [async.parallel](https://github.com/caolan/async#parallel) to good use. You know the commercial where you buy 1 suit at regular price and you get 3 suits free?  It's kinda like that... upload one file and we store 4 more formats for free.

Here is a list of the primary open source tools used:  Mongoose, Async, Busboy, Gridfs-stream and Sharp


<script src="https://gist.github.com/t2k/ae28bda9e194976ced03.js"></script>
