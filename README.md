JQUERY THINGBROKER PLUGIN

## VERSION

Plugin version: 0.3.0

## CHANGE LOG
2012.08.01-Roberto: First Version of this Requirements Document.
2012.08.09-Roberto: First Version of the plugin.
2012.10.29-Roberto: Second Version of the plugin using the new ThingBroker
2012.03.08-Roberto: Third Version of the plugin using the latest version of ThingBroker supporting thing-following


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

## THING THREADING BASED ON CONTEXT

An application might have several deployment "contexts" (e.g. a train station, a coffee shop, and a bar) simultaneously. Objects that listen to their "thing" representation need to be aware of their context and communicate only with "things" instantiated within such context. This jQuery plugin provides the functionality to thread applications through the URL parameter "display_id". Let's suppose we have an application hosted in:

```
(1) http://example.com/index.html. 
```

By visiting the url:

```
(2) http://example.dom/index.html?display_id=2
```

The jQuery plugin will create a thread that will not interfere with (1). In other words, users that interact with the application through (1) will not interfere with the interaction of those visiting (2).

If you will be deploying and application on several displays you should consider the Cherry Container (https://github.com/ubc-magic/container) which takes care of configuring and managing such threading.


## GLOBAL THING BROKER CONFIGURATION

Similar to "Thing Threading" this jQuery plugin provides the ability to configure the instance of ThingBroker that your application should use. This allows a single application to be deployed on top of different instances of ThingBroker without the need to change the application's code. The jQuery plugin looks for the URL parameter "thingbroker_url" to do this. Let's suppose we have an application hosted in:

```
(1) http://example.com/index.html. 
```

By visiting the url:

```
(2) http://localhost/demoapp.html?display_id=2&thingbroker_url=http%3A%2F%2Flocalhost%3A8080%2Fthingbroker
```

The jQuery plugin will create a thread that will not interfere with (1) and will use the url "http://localhost:8080/thingbroker" as endpoint to it's API REST calls. Please notice that a badly formed url will result in your application not being able to run.

If you will be deploying and application on several displays you should consider the Cherry Container (https://github.com/ubc-magic/container) which takes care of configuring and managing such threading.


## USAGE

Include jQuery > 1.7.2, and include the jquery.thingbroker.min.js plugin. For example:

```
<script type="text/javascript" src="jquery-1.7.2.min.js"></script>
<script type="text/javascript" src="jquery.thingbroker.min.js"></script>
```

If you want to be up-to-date without needing to worry about updates within your applications you can use our NON-CDN version of the jquery plugin (below), which is always kept up to date. This version depends on the thingbroker instance running in kimberly.magic.ubc.ca:8080/thingbroker.

```
<script type="text/javascript" src="http://kimberly.magic.ubc.ca:8080/thingbroker/resources/jquery-plugin/jquery.thingbroker.min.js"></script>
```

## JQUERY MANIPULATIONS API

Currently you have availability to four DOM operations with this plugin: append, prepend, remove and src. Append will append data to a brokerized DOM object, prepend will prepend data to a brokerize DOM object, while remove will remove such data. Src is only intended for updating the src value in an <img> object.

Given the following DOM objects:

```
<div id="id"></div>
<div id="id2"></div>
<img id="imageid" src="image.jpg"></img>
```

You can have access to the following JQUERY manipulations:

```
(#id).thing({"append": "<p>helloworld</p>", to: "#id2"})
(#id).thing({"prepend": "<p>helloworld</p>", to: "#id2"})
(#id).thing({"remove": "<p>helloworld</p>", to: "#id2"})
(#id).thing({"src": "http://otherhost/img.jpg", to: "#imageid"})
```

In order to being able to take advantage of this you need to tell the jQuery plugin which DOM objects must listen for events. You can do this by passing the flag parameter "listen" to your object. You can use the "url" parameter to specify a thingbroker instance of your choice.

```
(#id).thing({listen:true})
(#id).thing({listen:true, url: "http://yourownserver/thingbroker"})
```

The plugin takes the boolean parameters "container" and "debug". The container flag turns has a default value of "true", since we assume that developers want to take advantage of application threading. However, you might want to have a global application that is accessible throughout your whole deployment instances. In such case use the "container:false" flag. We also provide a "debug" flag that will print to your console all REST API calls made and threading manipulations.

```
(#id).thing({listen:true, container:false})
(#id).thing({listen:true, debug:true})
```


## LOW-LEVEL THINGBROKER ACCESS

In some cases jQuery manipulations will not suffice your purposes (e.g. if you want to access raw data from things). In such case this jQuery plugin provides direct access to the basic thingbroker methods.

To follow a thing you can use the function:

```
(#id).thing({"follow": "#id2"})
(#id).thing({"follow": "a_thingId"})
```

The following raw-methods are provided:

```
$.ThingBroker({url: "http:yourownserver/thingbroker"}).postThing("thingId")
$.ThingBroker().postEvent("thingId", {key:value})
$.ThingBroker().putEvent("eventId", "serverTimestamp", {key:value}) #UNRELEASED: WILL CHANGE!
$.ThingBroker().getEvents("thingId")
$.ThingBroker().getThing("thingId")
```

You have access to the following configuration parameters: debug, container, url. "debug" will print to the console all REST CALLS made and threading manipulations; "container" can be used to disable threading, and "url" can be used to force the usage of a thingbroker instance programatically.

## MANIPULATING DOM OBJECS FROM OUTSIDE THE JQUERY PLUGIN

If you're using raw events through your own custom plugin and want to communicate with DOM objects using the ThingBroker jQuery plugin these are the implemented key-data pairs.

+ THE EVENT {"append": "data"}
Appends "data" to a DOM thing. Take into account that data must contain the DOM type. For example, to append a "paragraph" to the object:

```
<div id="messages"></div>
```

You would need to send the following event to the "messages" thing. (IMPORTANT: if you are using thing-threading you must find your display_id value and modify your thingId appropriatedly. E.g. if you deploy an application under "display_id=2", you need to send the event to "messages2".

```
{"append": "<p>Hello there</p>"}
```

+ THE EVENT {"remove": "data"} (sent to thingId "message")
Removes the element that matches the given data from the object.

+ THE EVENT {"src": "data"} (sent to thingID "image")
Updates the src="" attribute of the image object:

```
<img src='data' id="#image"></img>
```

### CHANGES

Updating a PREVIOUS app:

+ change "jquery.broker.js" to "jquery.thingbroker.js"
+ the method "broker()" is now "thing()"
+ all DOM objects should be identified through "id" rather than "class"

+ To ensure nothing breaks use the broker-hosted jquery-plugin
<script type="text/javascript" src="http://kimberly.magic.ubc.ca:8080/thingbroker/resources/jquery-plugin/jquery-1.7.2.min.js"></script>
<script type="text/javascript" src="http://kimberly.magic.ubc.ca:8080/thingbroker/resources/jquery-plugin/jquery.thingbroker.min.js"></script>
<script type="text/javascript" src="http://kimberly.magic.ubc.ca:8080/thingbroker/resources/jquery-plugin/processing-1.4.1.min.js"></script>
