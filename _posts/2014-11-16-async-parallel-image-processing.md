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

Streams in NodeJS are incredibly powerful.  I found out for myself for a site I'm working on where users are allowed to upload images from their mobile devices. Images are typically from a camera photo but they can also be selected from local gallery or perhaps from clould storage.  Desktop users can use the 'file-open' diaglog.
 
The code below is request handler written in CoffeeScript that handles a mult-part http POST (containing a file) from an Express route.  Steams are modern, instead of writing temp files to the file-system, we stream a file into a Mongdb grid-fs filestore.  Streams are great because not only does a file not touch a file system, they are processed in a non-blocking asyncronous fashion. A stream doesn't have to process a large file in one buffer either which allows a Node/Express server to use memory more efficiently.  With Busboy the file stream pipes it's way straight into a NoSQL datastore.  My original goal was to upload files into a MongoDB gridstore and I was gonna stop right there and call it a day, problem solved.  I'd achieved my goal, good night, Bob's your Uncle! I can quit now.  But nooo, I'd been reading [Async JavaScript](https://pragprog.com/book/tbajs/async-javascript) and was very impressed by the [Async.parallel](https://github.com/caolan/async#parallel) example but I didn't have a good use case.  As we began early testing of our app it quickly became apparent our need to have multiple versions of each image.  Perhaps versions more suitable for Desktop, Tablet, Mobile and maybe even Thumb images. You know that commercial where you buy 1 suit at regular price and you get 3 suits free?  Well Async.Parallel is kinda like that... upload one file and we store 4 more formats for free.

Here is a list of the primary ***open-source*** tools used herein:  [Mongoose](#), [Async](https://github.com/caolan/async), [Busboy](#), [gridfs-stream](#) and perhaps the most awesome [sharp!](#)


<script src="https://gist.github.com/t2k/ae28bda9e194976ced03.js"></script>

*For extra-bonus points: What does the 201/created JSON response look like?*

