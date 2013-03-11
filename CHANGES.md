Updating an app:

+ change "jquery.broker.js" to "jquery.thingbroker.js"
+ the method "broker()" is now "thing()"
+ all DOM objects should be identified through "id" rather than "class"

+ To ensure nothing breaks use the broker-hosted jquery-plugin
<script type="text/javascript" src="http://kimberly.magic.ubc.ca:8080/thingbroker/resources/jquery-plugin/jquery-1.7.2.min.js"></script>
<script type="text/javascript" src="http://kimberly.magic.ubc.ca:8080/thingbroker/resources/jquery-plugin/jquery.thingbroker.min.js"></script>
<script type="text/javascript" src="http://kimberly.magic.ubc.ca:8080/thingbroker/resources/jquery-plugin/processing-1.4.1.min.js"></script>
