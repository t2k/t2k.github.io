---
layout: post
title: "Async Parallel Image Processing"
description: ""
tagline: "--server-side"
category: [server-side]
tags: [async, CS, mongoose, busboy, gridfs-stream]
---
{% include JB/setup %}

###Processing file updloads on the server.

Store files on the backend in mongoDB grid filestore.  In addition to storing the original RAW file, let's also create a Desktop, Tablet, Mobile and Thumb version.  All processing is done in parallel.

<script src="https://gist.github.com/t2k/ae28bda9e194976ced03.js"></script>
