---
layout: post
title: "Endless Scrolling in Backbone.Marionette"
description: "customizing behavior..."
tagline: "--custom collection methods"
category: [client-side] 
tags: [Backbone.Collection, CS, MVC]
---
{% include JB/setup %}
###MVC - [MODEL](http://backbonejs.org/#Collection) [VIEW](http://marionettejs.com/docs/marionette.compositeview.html) [CONTROLLER](http://marionettejs.com/docs/marionette.controller.html)

The primary goal when creating modular client side architecture is separation of responsibilities between views and the data they use in their presentations.

In this example, The abstract MODEL is a backbone collection wrappging an array of data returned from an AJAX call.  The abstact VIEW is a class extending from Backbobne.CollectionView that wraps an HTML template.  The abstract CONTROLLER exends from a Marionette.Controller class.  The controller facilitates event driven communication between the MODEL <==> VIEW and coordinates data marshalling between the REST-API and the VIEW presentation.


<script src="https://gist.github.com/t2k/dc5300be5343524656fa.js"></script>
