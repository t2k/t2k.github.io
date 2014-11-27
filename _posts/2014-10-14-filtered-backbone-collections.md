---
layout: post
title: "Endless Scrolling in Marionette"
description: "customized backbone collection behavior"
category: [client-side] 
tags: [Backbone, Marionette, MVC, CoffeeScript, AMD, RequireJS]
---
{% include JB/setup %}

####MVC - [MODEL](http://backbonejs.org/#Collection) [VIEW](http://marionettejs.com/docs/marionette.compositeview.html) [CONTROLLER](http://marionettejs.com/docs/marionette.controller.html)


My primary goal when developing a client-side web application is to establish a clear separation of responsibilities between **views** and **model** data. In the world of async web apps, a strict definition of the role played by the ***controller*** can get a little *fuzzy.* But I digress... In this example, I'm using the [AMD](http://requirejs.org/docs/whyamd.html#amd) specification.   and by doing so it enforces modular design concept built into the application architecture. Each module has its own self contained scope and cannot communicate directly with other modules outside of it's intended scope. This  approach may seem like overkil, especially when designing a simple demo, however as client side complexity grows the AMD 'building-block' approach pays off.

####Feeling the MVC Backbone.Marionette Flow

There is so much out there on the Internet about **MVC**.  If you Google **Model-View-Controller** MVC, MVP, MVVC, MVVM etc. you'll be overwhelmed.  I've read most of it and I'm sure you have too.  There's so many perpectives and opinions out there about MVC and I'm not here to solve the debate. Every developer faces unique complexities and must approach their application architecture by taking their own full stack into consideration.  We face different complexities whether we live in a JAVA, .NET or Linux/Open Source world. Furthermore, realities change if we're using an enterprise database such as  Oracle, SQL-Server, Postgress, MySQL or if you live in a NoSQL world like MongoDB, Couch, Pouch, Slouch etc. Let's abstract all the server-side issues away and just assume the back-end in this example is a fully supported REST-API so we can focus on the client-side development.

This post is about client-side development and we're ***MVC-flowing*** with Backbone.Marionette. The abstract **MODEL** is a backbone collection, wrapping an array of data returned from an AJAX call. In Backbone, the MODEL has the responsibilty of making async/AJAX calls against the REST API's, that's the Backbone way.  The abstact **VIEW** is a class extending from Backbobne.CompositeView wrapping an HTML template which defines a user-interface.  The abstract **CONTROLLER** exends from the Marionette.Controller class. The controller creates views and coordinates passing data to those views.  The controller also sets up the intra-module, event driven communication between the MODELs and the VIEWs. The controller coordinates the event-api handling and data-marshalling between the REST-API and the VIEW presentation architecture.  Have I lost you yet?  I'm a little confused too, hopefully a diagram will help.


####The VIEW: A Scrollable Region
The hardest part for me was understanding the markup and CSS required to make a scrollable region work within a Marionette View. Working with click events, change events, blur events is straight forward. Understanding these user interactions is pretty obvious. In order to create a UI that will emit scroll events will require a bit more explicit HTML and CSS.  The interaction between the *html* in our template and the Marionette View class that repsponds to scroll events took a little extra level of understanding on my part.  For this to work, we must basically contrain a `div` with a `max-height` setting then have that wrap in inner `div`

#### HTML
~~~ html
<div class="scrollable-container">
  <div class="scrollable-inner">
  <!-- repeatable item views --> 
  </div>  
</div>
~~~

#### CSS
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

###The CompositeView Class:

~~~

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
    margin = .05 * virtualHeight  
    scrollTop = @$el.scrollTop() + @$el.height()
    @trigger "scroll:more:lpitems" if (scrollTop + margin) >= virtualHeight

~~~

Notice above, the extended CompositeView class wraps its template like this:

~~~
  className:            "scrollable-container"
~~~

This interaction between the view's rendered template and it's CSS is critical in enabling the view both emit scroll from it's markup and listen to events. What we are doing here is wrapping a view in a contrained block element.  By setting a ``max-height:500px`` and ``overflow-y:scroll`` this enables the view to both emit the scroll event and the view's events hash

```coffeescript
  events: 
    "scroll": "checkScroll"
```

can listen for a scroll event and respond with it *checkSroll* event.  The logic: if the scroll bar is close to the bottom, trigger the ``scroll:more:items`` event.  That's all we need to support endless scroll.

<br/>

####Screenshot: Endless Scroll UI

<div class="row">
  <div class="col-sm-12">
    <img src="/assets/images/scroll-region.png" class="img-responsive" />
  </div>
</div>


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

#### Templates Module
~~~
define (require) ->
    heatmap:    require "text!apps/lp/list/templates/heatmap.htm"
    lpItem:     require "text!apps/lp/list/templates/lpItem.htm"
    lpItems:    require "text!apps/lp/list/templates/lpItems.htm"
    layout:     require "text!apps/lp/list/templates/layout.htm"
    panel:      require "text!apps/lp/list/templates/panel.htm"
    title:      require "text!apps/lp/list/templates/title.htm"
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

### MVC Interactions
Understanding the specific interactions among the Model, View, and Controller modules is the key to understanding the larger application architecture.  Communication among modules is event driven which also enables us to decouple out modules.  Starting with the backbone **Model**, it makes async requests to a REST/API as shown here by setting up a request handler on application's `msgBus`: 

    getLPEntities: (options={}, filter={})->
      {skip,take} = options
      _coll.setPage skip
      _coll.setPageSize take
      _coll.setFilter filter
      _.extend options, filter
      _coll.fetch
        reset: true
        data: options
        _coll

    ...
 
    msgBus.reqres.setHandler "lp:entities", ->
      API.getLPEntities
        skip: 0
        take: 20
 

The **Controller**, instantiated by a modular App, is responsible for requesting data from the **Model** and passing that data to the **View** as shown here from the **controller** initialize function:

    class Controller extends AppController
      initialize:->
        @lpEntities = msgBus.reqres.request "lp:entities"
        heatmaps = msgBus.reqres.request "lpheatmap:entities"

        @layout = @getLayoutView()
        @listenTo @layout, "show", =>
          @heatMapRegion heatmaps
          @titleRegion()
          @panelRegion()
          @lpRegion()
        @show @layout,
          loading:
            entities: [@lpEntities, heatmaps]

      lpRegion: ->
        lpView = new Views.LPList
          collection: @lpEntities

        @listenTo lpView, "childview:lp:item:clicked", (child, args) ->
          msgBus.events.trigger "lp:item:clicked", args.model
        @listenTo lpView, "scroll:more:lpitems", ->
          msgBus.reqres.request "lp:fetch:more"

        @layout.listRegion.show lpView
           
  

The **View** listens at UI level for events triggered by specific user interactions, in this specific case the view is responding to a `scroll` event and the *View* triggers an event for the controller to handle. 

    LPList: class _listview extends AppViews.CompositeView
      template: _.template(Templates.lpItems)
      childView: LPItem
      childViewContainer: ".scrollable-inner"
      className: "scrollable-container"

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
        margin = .05 * virtualHeight # 10%
        scrollTop = @$el.scrollTop() + @$el.height()
        @trigger "scroll:more:lpitems" if (scrollTop + margin) >= virtualHeight



 to the *controller* and it gets to decide how to pass event's to models or in other cases to routeable apps.  In this specific case *scroll* events trigger a ``scroll:more:lpitems`` event up the chain.  

As seen above, the **controller** monitors this event like this:

~~~
@listenTo lpView, "scroll:more:lpitems", ->
        msgBus.reqres.request "lp:fetch:more"
~~~

Here, the *controller* listens to events from it's *views* and passes them up the chain.  In our case the ***LP*** entity model listens to this custom event and springs into action, by fetching another page of items.  That's all there is to it.  The extended Marionette.CompositeView automagically updates it's childview item template because it listens to the @collection ``reset`` event causing it to renders itself with *more:items*

...