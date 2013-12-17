---
title: Home | OANDA API
---

# API Home

* TOC
{:toc} 

We developed our API on top of our [award winning](http://www.forexcrunch.com/forex-magnates-summit-oanda-wins-best-forex-broker-award/) 
currency trading platform, [fxTrade](http://fxtrade.com). 


What can I do with the OANDA REST API?
--------------------------------------

#### Market Data

Get real-time currency rates on over 90 currency pairs. Monitor the forex market for changes in real-time, 24 hours a day. You will have access to historical currency rates, dating back over 10 years.

#### Trading

Place trades and orders with our trading API.  You can fetch account activites, balance, trades and orders.

----

API URL
--------------------

Our URL for our sandbox is 
    
    http://api-sandbox.oanda.com

Sandbox environment is a test bed used to showcase our upcoming REST API and is open to everyone to use.  Sandbox environment currently has the following limitation:

* Generated (fake) market data
* No order monitoring.  Limiting orders will not be triggered. 

OANDA API on production is currently in a closed beta period with a limited number of slots.  We will be opening up our beta program in the near future.  

----

Request and Response
--------------------

All requests require <code>Content-Type: application/x-www-form-urlencoded</code> unless specified otherwise.

All responses will be in [JSON format](http://www.json.org).

----

Errors
------

When an error occurs, the applicable HTTP response code is returned as well as an error message in the body in the following format:

    HTTP/1.1 400 Bad Request

~~~json
{
    "code" : [OANDA error code, may or may not be the same as the HTTP status code],
    "message"   : [a description of the error which occurred, intended for developers],
    "moreInfo"  : [(OPTIONAL)a link to a web page describing the error and possible causes and solutions]
}
~~~

[Click here for more on error codes.](/docs/v1/troubleshooting)

----

Rate Limiting
-------------

Client is allowed to have no more than 4 requests per second on average, with bursts of no more than 5 requests. Excess requests will be delayed on our server.

----

How do I start?
---------------

* Read the [Getting Started Guide](/docs/v1/getting-started/)
* Try some [Sample Code](/docs/v1/code-samples/)
* Try the API using the [API Console](https://apigee.com/oandapoc/embed/console/oanda)
* Follow [@oandaapi](http://twitter.com/oandaapi) on Twitter
* Email us at api@oanda.com with any questions 


[![githalytics.com alpha](https://cruel-carlota.pagodabox.com/08c4e77e4cb54028197e21a0923e9311 "githalytics.com")](http://githalytics.com/oanda/apidocs)

