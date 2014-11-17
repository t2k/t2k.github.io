---
layout: post
title: "Endless Scrolling in Backbone.Marionette"
description: "customizing behavior..."
tagline: " -- customized collection behavior"
category: [client-side] 
tags: [Backbone.Collection, CS, MVC, AMD]
---
{% include JB/setup %}

##An MVC - [MODEL](http://backbonejs.org/#Collection) [VIEW](http://marionettejs.com/docs/marionette.compositeview.html) [CONTROLLER](http://marionettejs.com/docs/marionette.controller.html) approach


The primary goal when building a modular client-side architecture is to establish a separation of responsibilities between views and data. The [AMD-Asyncronous Modular Definition](http://requirejs.org/docs/whyamd.html#amd) specification enforces a decoupled modular approach to our application design. Each module has its own self contained scope that cannot communicate directly with other modules outside of it's intended scope.

In this post, The abstract MODEL is a backbone collection, wrappging an array of data returned from an AJAX call.  The abstact VIEW is a class extending from Backbobne.CompositeView, wrapping an HTML template that defines a user-interface (UI).  The abstract CONTROLLER exends from the Marionette.Controller class. The controller creates views and passes data to those views and sets up the intra-module, event driven communication between the MODELs and the VIEWs. The controller also coordinates the event-api handling the data marshalling between the REST-API and the VIEW presentation architecture.

###Diagram: Backbone.Marionette Model - View - Controller 
![Diagram](bbm-mvc-diagram.jpeg)

---

<div role="tabpanel">
  <!-- Nav tabs -->
  <ul class="nav nav-tabs" role="tablist">
    <li role="presentation" class="active"><a href="#model" aria-controls="model" role="tab" data-toggle="tab">MODEL</a></li>
    <li role="presentation"><a href="#view" aria-controls="profile" role="tab" data-toggle="tab">VIEW</a></li>
    <li role="presentation"><a href="#controller" aria-controls="messages" role="tab" data-toggle="tab">CONTROLLER</a></li>
  </ul>

  <!-- Tab panes -->
  <div class="tab-content">
    <div role="tabpanel" class="tab-pane fade in active" id="model">
  		<script src="https://gist.github.com/t2k/dc5300be5343524656fa.js"></script>
		</div>
    <div role="tabpanel" class="tab-pane fade" id="view">
    	<script src="https://gist.github.com/t2k/ccde4a35397ee661b47b.js"></script>
    </div>
    <div role="tabpanel" class="tab-pane fade" id="controller">
    	<script src="https://gist.github.com/t2k/762d37c61fb7ab4b62cc.js"></script>			
    </div>
  </div>
</div>

---

### Templates Module

~~~
define (require) ->
    heatmap:    require "text!apps/lp/list/templates/heatmap.htm"
    lpItem:     require "text!apps/lp/list/templates/lpItem.htm"
    lpItems:    require "text!apps/lp/list/templates/lpItems.htm"
    layout:     require "text!apps/lp/list/templates/layout.htm"
    panel:      require "text!apps/lp/list/templates/panel.htm"
    title:      require "text!apps/lp/list/templates/title.htm"
~~~


###lpitems.htm
The CompositeView template:
**Very Important!** the extended CompositeView 
class wraps its template in a ``div.scrollable-container`` tag. This layout and CSS is 
crucial to enabling the view to trigger scroll events.  What we are doing here is wrapping
a view in a contrained block element.  By setting a ``max-height:500px`` and ``overflow-y:scroll``
it enables the view to trigger the custom scroll events and our view can listen via it's own 
checkSroll event.  

	LPList: class _listview extends AppViews.CompositeView
	  template: _.template(Templates.lpItems)
	  childView:            LPItem
	  childViewContainer:   ".scrollable-inner"
	  className:            "scrollable-container"
	  ui:
	    scroll: ".scrollable-inner"
	              
	  events: 
	    "scroll": "checkScroll"
	    
	  collectionEvents:
	    "request": ->
	      NP.start()
	      NP.inc()

	    "sync error": ->
	      NP.done()

	  checkScroll: (e) =>
	    virtualHeight = @ui.scroll.height()
	    margin = .05 * virtualHeight  # 10%
	    scrollTop = @$el.scrollTop() + @$el.height()
	    #console.log "scroll", margin, virtualHeight, scrollTop if (scrollTop + margin) >= virtualHeight
	    @trigger "scroll:more:lpitems" if (scrollTop + margin) >= virtualHeight




~~~
<div class="scrollable-inner">
</div>
~~~

####CSS rules
~~~
// marginal support of endless scrolling
.scrollable-container {
  max-height: 500px;
  overflow-y: scroll;
}
.scrollable-inner {
  max-height:100%;
}

@media all and (min-width: 1023px) {
  .scrollable-container {
    max-height: 640px;
  }
}

~~~


lpitem.htm:  the ItemView template

~~~
<div class="row">
    <ul class="list-group col-md-3 col-sm-6 col-xs-12">
      <li class="list-group-item"><span class="label {{ (LPType==='BF' || LPType=='EM') ? 'label-danger': (LPType=='IF'|| LPType=='NF') ? 'label-success' : 'label-warning' }}"><strong>{{LPType}}</strong></span><span title="process id"> {{id}}</span></li>
      <li class="list-group-item">{{CCY}}</li>
      <li class="list-group-item"><span title="processed">{{moment(TimeStampUTC).fromNow()}} </span><span title="value date"> {{moment(Savedate).format("L")}}</span></li>
    </ul>
    <ul class="list-group group col-md-3 col-sm-6 col-xs-12">
      <li data-toggle="tooltip" title="SAP-CML#" class="list-group-item">{{SAPCML}}</li>
      <li class="list-group-item">{{Counterparty}}</li>
      <li class="list-group-item">{{Portfolio}}</li>
    </ul>
    <ul class="list-group group col-md-3 col-sm-6 col-xs-12">
      <li data-toggle="tooltip" title="Murex Simple TradeID" class="list-group-item">{{TradeID}}</li>
      <li data-toggle="tooltip" title="Maturity-Date" class="list-group-item">{{moment(OldEndDate).format("L")}}</li>
      <li data-toggle="tooltip" title="Loan-Amount: Change" class="list-group-item">Change<span class="badge">{{Globalize.format(ChangeOutstanding,'n')}}</span></li>
    </ul>
    <ul class="list-group group col-md-3 col-sm-6 col-xs-12">
      <li class="list-group-item">{{ScheduleIndex}} <span data-toggle="tooltip" title="cash flows affected" class="badge">{{FlowCount}}</span></li>
      <li data-toggle="tooltip" title="LS vs LPR" class="list-group-item">&nbsp<span class="badge">{{Globalize.format(LS,'n3')}} v {{Globalize.format(LPR,'n3')}}</span></li>
      <li data-toggle="tooltip" title="PV'd Loss/Benefit" class="list-group-item">PV<span class="badge">{{Globalize.format(PVLossBenefit,'n')}}</span></li>
    </ul>
</div>
~~~
