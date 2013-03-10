JQUERY THINGBROKER PLUGIN

## VERSION

Plugin version: 0.3.0
Broker dependency: < 1.4.0

## CHANGE LOG
2012.08.01-Roberto: First Version of this Requirements Document.
2012.08.09-Roberto: First Version of the plugin.
2012.10.29-Roberto: Second Version of the plugin using the new ThingBroker
2012.03.08-Roberto: Third Version of the plugin using the latest version of ThingBroker supporting following


## INTRODUCTION

This plugin serves to augment existent web content with real-time capabilities pertaining to Natural User Interfaces. The broker.jquery plugin connects web document objects (divisions, paragraphs, lists) together without the need of server-dependent background processes. This allows for client-to-client interaction and web-browser to web-browser data exchange that is transparent to the developer. The plugin leverages JQuery's wide range of hooks and functions to manipulate data.

## SCENARIOS

Mollie stands before a large display and wants to move content from a handheld device onto the large screen. To do so she performs a natural gesture of "throwing" the content on her device towards the screen.

## CLIENT-TO-CLIENT SOLUTION

Mollie is moving content from one web-browser (her mobile) to another web-browser (the one running a large display application), often both applications are developed in different languages (ruby, php, java) and connecting them together requires server-dependent hooks and listeners. By taking the mental model literally we propose exposing web-clients to each other through a single third party service without the need of server-dependent modifications. This allows for ANY web-browser, service, or application to modify and access shared data seamingless and to make development of small applications a snap.

## DEPENDENCIES

The current plugin depends on the Thing Broker (https://github.com/magic-ubc/thingbroker). You can run a self version of the broker on your local machine, or use the provided testing version available at http://kimberly.magic.ubc.ca:8080/thingbroker. Downloading this project and running the examples will work out of the box, as the testing version of the Thing Broker is the default backend for this plugin.

## SECURITY CONCERNS AND SCALABILITY

This solution is targeted at prototype and small applications. In a large application having numerous inter-connected objects across many applications, the number of listening objects might become insecure. Please take this into consideration when developing your applications.

## DESIGN REQUIREMENTS

* DOM objects are considered "things" on the web
* Default callbacks are provided for basic common DOM operations (e.g. append, remove) in JQuery, making object-to-object manipulations transparent.

## ARCHITECTURE
```
  Large display App                                            Small display App
-----------------------           ---------------             --------------------
      DOM Object                   Non-web Client                  DOM Object
-----------------------           ---------------             --------------------
     Jquery plugin. <------------>  ThingBroker   <---------->      Jquery plugin
-----------------------           ---------------             --------------------
      Jquery api                   DB      QUEUE                  Jquery api
------------------------          -----    ------             --------------------
      JavaScript                                                   Javascript
```
Both applications might be served from the same server, and even the same application but by binding them through a broker we inter-connect DOM objects, rather than applications, that allow for inter-object communications, e.g. one swipe on one object manipulates another object in real time.

```
[Object 1]           ----------------[Object 2]
   |                |
   | Listens to     | Posts event        | Listens to
   |                |                    |
[Thing 1]    <-------                [Thing 2]

```

## API

Currently you have availability to four DOM operations with this plugin: append, prepend, remove and src. Append will append data to a brokerized DOM object, while remove will remove such data. Src is only intended for updating the src value in an <img> object.

Given the following DOM objects:

```
<div id="id"></div>
<div id="id2"></div>
<img id="imageid" src="image.jpg"></img>

```

You can have access to the following JQUERY manipulations:

```
JQUERY EVENTS
(#id).thing({"append": "<p>helloworld</p>", to: "#id2"})
(#id).thing({"prepend": "<p>helloworld</p>", to: "#id2"})
(#id).thing({"remove": "<p>helloworld</p>", to: "#id2"})
(#id).thing({"src": "http://otherhost/img.jpg", to: "#imageid"})

LISTENER THING
(#id).thing({listen:true})
(#id).thing({listen:true, url: "http://yourownserver/thingbroker"})
```

If you want to have direct access to manipulating "things", or accessing their data you can make use of the following methods:

```
OTHER METHODS
(#id).thing({"follow": "#id2"})

THINGBROKER API METHODS (BUNDLED WITH THE JQUERY PLUGIN)

$.ThingBroker({url: "http:yourownserver/thingbroker"}).postThing("thingId")
$.ThingBroker().postEvent("thingId", {key:value})
$.ThingBroker().getEvents("thingId")
$.ThingBroker().getThing("thingId")
```


$('.class').broker(); Will subscribe the object with a class "class" to itself, i.e. a topic="class" and a clientID="class"

$('.class').broker({listen:true}); Will subscribe the object, set repetitive ajax calls to listen for events, and define callback functions for append, remove and src.

$('.class').broker({listen:true, url:'http://site.com'}); Will initialize the object to listen on a broker hosted on http://site.com

$('.class').broker({listen:true, url:'http://site.com', topic:'atopic'}); Will initialize the object to listen on a broker hosted on http://site.com and listen on a different object's topic (to create a listener object for another topic).

$('.class').broker({append:'hello this is a message', to: 'class2'}); Will append 'hello this is a message' to the object with class "class2" (this will generate a p object within a div element, or a li element within a ul element)

$('.class').broker({remove: 'this is a data sent previously', to: 'class2'}); Will remove child objects that contain the text "this is a data sent previously" within the object with class "class2"

$('.class').broker({src:'imagesrc', to: 'class2'}); Will update the src parameter of the img object with class "class2"



## IMPLEMENTED KEY-DATA PAIRS AND THEIR DOM OPERATIONS

If you're using raw events through your own custom plugin and want to communicate with objects using the Thing Broker Jquery plugin these are the implemented key-data pairs (more info at https://github.com/magic-liam/thingbroker)

```
THE EVENT {"append": "data"}
Appends "data" to a DOM thing. Take into account that data must contain the DOM type. For example, to append a "paragraph" the object <div id="messages"></div>, you would need to send the event: {"append": "<p>Hello there</p>"}

```

```
THE EVENT {"remove": "data"}
Removes the element that matches the given data from the object listening to the event.
```

```
THE EVENT {"src": "data"}
Updates the src="" attribute of an image object listening to the event: <img src='data'></img>
```

