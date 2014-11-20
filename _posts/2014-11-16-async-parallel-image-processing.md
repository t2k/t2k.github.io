---
layout: post
title: "async.parallel image processing"
description: "streaming files to mongodb"
tagline: "serve side image processing"
category: [server-side]
tags: [mongodb, busboy, gridfs-stream, async, mongoose, nodejs, sharp, coffee-script]
---
{% include JB/setup %}

###Processing file updloads on the server.

Streams in NodeJS are incredibly powerful.  I found out myself for a site I'm working on where users are allowed to upload images from their mobile devices. Images are typically sent from a camera photo but they can also be selected from a local gallery or clould storage.  Desktop users can use the 'file-open' diaglog.
 
The code below is a request handler written in coffee-script that handles a mult-part http POST (containing a file) from an Express route.  Steams are modern, async and event-driven. Instead of writing temp files to the file-system, we stream the file into a [mongoDB GridStore](http://mongodb.github.io/node-mongodb-native/markdown-docs/gridfs.html). Streams are great for uploads, not only because the doesn't touch a disk file-system but they are processed in a non-blocking, asyncronous fashion. A stream doesn't have to process a large file in one buffer either which allows a server to use memory more efficiently. With [busboy](https://github.com/mscdex/busboy) the file stream pipes it's way straight into mongoDB via [mongoose](http://mongoosejs.com/).  My original goal was to upload files into a mongoDB GridStore and I was gonna stop right there and call it a day, problem solved.  I'd achieved my goal, in fact I'd gone one better and used streams.  Good night, Bob's your Uncle! We can quit now.  But nooo, I'd been reading [Async JavaScript](https://pragprog.com/book/tbajs/async-javascript) and was very impressed by the [Async.parallel](https://github.com/caolan/async#parallel) example but I didn't have a good use case. Plus, I have a kid in college and wanted to impress him with my open-source chops.  As we began early testing of our app it quickly became apparent the need to support multiple platforms with multiple versions of each image.  We needed versions more suitable for Desktop, Tablet, Mobile and  Thumb images. You know that commercial where you buy 1 suit at regular price and you get 3 suits free?  Well [sharp](https://github.com/lovell/sharp) image processing with **Async.Parallel** is kinda like that... upload one file and store 4 more formats for free.  I hope the kid likes it!


<script src="https://gist.github.com/t2k/ae28bda9e194976ced03.js"></script>

*For extra-bonus points: What does the 201/created JSON response look like?*

