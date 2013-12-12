---
title: Getting Started | OANDA API
---

# Getting Started Guide

* TOC
{:toc}


<!--
## REST Basics

The [Trading API reference documentation](/docs/v1/reference/#trading-api-overview) describes the URI structure as follows:

##### Base URL
[http://api-sandbox.oanda.com/v1/](http://api-sandbox.oanda.com/v1/)

All requests on the sandbox will use this as the base URL.

##### Accounts
[http://api-sandbox.oanda.com/v1/accounts/6531071](http://api-sandbox.oanda.com/v1/accounts/6531071)

Your account id is always the base URI to any trading request.  If you issue a GET request with this URL, you'll see details for account 6531071.

##### Collection
[http://api-sandbox.oanda.com/v1/accounts/6531071/trades](http://api-sandbox.oanda.com/v1/accounts/6531071/trades)

Every account has trades, so if you issue this as a GET request, you'll get a list of currently open trades for that account.

##### Resource
[http://api-sandbox.oanda.com/v1/accounts/6531071/trades/177810368](http://api-sandbox.oanda.com/v1/accounts/6531071/trades/177810368)

If you already have a unique id for a trade, you can issue this as a GET request to get more details about trade 177810368.

Each URI performs a different function depending on whether you issue a GET, POST, PUT, or DELETE request.
-->

------

There are three main things you can do with the REST API:

1. Get real time currency prices
1. Get historical currency prices and charts
1. Trade currencies, metals, and CFDs on OANDA forex trading accounts

All requests and responses are encoded in the [JSON format](http://www.json.org/).

## Get real time currency prices

Currencies, metals and CFD prices change multiple times per second. To get a price, specify the instrument name you want to retrieve, for
example EUR/USD.  Replace the '/' character with an underscore '_' in currency pair names.

#### Example
Get the current price of EUR/USD

    GET http://api-sandbox.oanda.com/v1/quote?instruments=EUR_USD 

Response:

~~~json
{
  "prices" : [
    {
      "instrument" : "EUR_USD",
      "time" : "2013-09-16T18:59:03.687308Z",
      "bid" : 1.33319,
      "ask" : 1.33326
    }
  ]
}
~~~

#### What currencies, metals, and CFDs are available?

Get the list of available instruments

    GET http://api-sandbox.oanda.com/v1/instruments

Response:

~~~json
{
  "instruments":
  [
    {
      "instrument":"AUD_CAD",
      "displayName":"AUD/CAD",
      "pip":"0.0001",
      "maxTradeUnits":10000000
    },

    ...

    {
      "instrument":"ZAR_JPY",
      "displayName":"ZAR/JPY",
      "pip":"0.01",
      "maxTradeUnits":10000000
    }
  ]
}
~~~

#### Sample Code
[Simple Rate Panel](https://github.com/oanda/simple-rates-panel) is written in Javascript, and gives you the current price for a chosen currency pair.  Check out a live version [here](http://oanda.github.com/simple-rates-panel/simplepanel.html).

[Android Rates](https://github.com/oanda/AndroidRatesAPISample) is written in java, and uses the API to show current prices.

#### Reference
[Reference documentation](https://github.com/oanda/apidocs/blob/master/sections/reference.md#price-api-overview) for real-time currency prices.

## Experimental WebSocket Streaming API

#### Example

    var socket = io.connect('http://api-sandbox.oanda.com' , {
      resource : 'ratestream'
    });
    
    socket.emit('subscribe', {'instruments': ['EUR/USD','USD/CAD']});
    
    socket.on('tick', function (data) {
      console.log("Received tick:" + JSON.stringify(data));
    });
    
#### Sample Code
[Streaming API Demo](https://github.com/oanda/streamingapi-demo)

#### Reference
[Reference documentation](https://github.com/oanda/apidocs/blob/master/sections/streaming.md) for the streaming API

## Get historical prices and charts

The API can also be used to get both current and historical candles for a variety of uses, including creating your own charts.

#### Example
Get two of the most recent candles for EUR/USD
  
    http://api-sandbox.oanda.com/v1/instruments/EUR_USD/candles?count=2

Response:

~~~json
{
  "instrument":"EUR_USD",
  "granularity":"S5",
  "candles":
  [
    {
      "time":1354226425,
      "openMid":1.29797,
      "highMid":1.29797,
      "lowMid":1.29797,
      "closeMid":1.29797,
      "complete":"true"
    },
    {
      "time":1354226450,
      "openMid":1.29795,
      "highMid":1.29795,
      "lowMid":1.29794,
      "closeMid":1.29794,
      "complete":"false"
    }
  ]
}
~~~

#### Sample Code
[Candle Average Price](https://github.com/oanda/cl-restapi-demo) is written in Lisp, and will calculate the average price of a currency pair over the past 'X' days.

#### Reference
[Reference documentation](https://github.com/oanda/apidocs/blob/master/sections/reference.md) for historical prices and charts.


## Trade currencies, metals, and CFD's

### Create a Test User

To test your app on the API sandbox, you first need to create a test user.  A user owns accounts, and accounts are what hold the money you’re using to trade.  When you want to place a trade, you must specify the account containing the funds you wish to trade on.

Simply [generate a user and an account](http://oanda.github.com/gen-account.html).  You will be given a username, password and an account id.

* The account id is required as a parameter to any requests related to making trades or getting trade information

The username and password can, in most cases, be thrown away.  We wanted to make it easy for people to trade as quickly as possible in our sandbox and so the API is not authenticated.  **The account id is the only thing you need to trade.**

If you want to [generate a user and an account](http://oanda.github.com/gen-account.html) yourself, you can follow [these steps](/docs/v1/accounts/#create-a-test-account).

### Opening a trade

#### Example
Open a buy EUR/USD trade for 1000 units.  This example uses curl to submit three parameters using POST data.

    $ curl -X POST -H ""-d "instrument=EUR_USD&units=1000&side=buy&type='market'" http://api-sandbox.oanda.com/v1/accounts/6531071/orders

Response:

###### Header

~~~header
HTTP/1.1 200 OK
~~~

###### Body
~~~json
{

  "instrument" : "EUR_USD",
  "time" : "2013-12-06T20:36:06Z", // Time that order was executed
  "price" : 1.37041,               // Trigger price of the order
  "tradeOpened" : {
    "id" : 175517237,              // Order id
    "units" : 1000,                // Number of units
    "side" : "buy",                // Direction of the order
    "takeProfit" : 0,              // The take-profit associated with the Order, if any
    "stopLoss" : 0,                // The stop-loss associated with the Order, if any
    "trailingStop" : 0             // The trailing stop associated with the rrder, if any
  },
  "tradesClosed" : [],
  "tradeReduced" : {}
}
~~~

#### Sample Code
[Api Trading](https://github.com/oanda/py-api-trading) is written in Python, and demonstrates opening trades and orders.

#### Reference
[Reference documentation](/docs/v1/trades) for opening trades and orders.

<!--
## Get existing open trades

#### Example
Get the list of open EUR/USD trades for account 6531071.

    http://api-sandbox.oanda.com/v1/accounts/6531071/trades?instrument=EUR_USD

Response:

~~~json
{
  "trades" : [
    {
      "id" : 177810427,
      "units" : 1000,
      "side" : "buy",
      "instrument" : "EUR_USD",
      "time" : "2013-01-11T15:57:11Z",
      "price" : 1.29787,
      "takeProfit" : 0,
      "stopLoss" : 0,
      "trailingStop" : 0
    },
    {
      "id" : 177810261,
      "units" : 4,
      "side" : "buy",
      "instrument" : "EUR_USD",
      "time" : "2013-01-11T15:57:11Z",
      "price" : 1.29736,
      "takeProfit" : 0,
      "stopLoss" : 0,
      "trailingStop" : 0
    }
  ],
}
~~~

#### Sample Code
[Account Search](https://github.com/oanda/AccountSearchPHP) is written in PHP, and demonstrates listing an account's open trades and orders.

#### Reference
[Reference documentation](https://github.com/oanda/apidocs/blob/master/sections/reference.md#trading-api-overview) for opening trades and orders.

### Get open positions

Open positions are an aggregated view of your open trades.  Rather than average out all of your open trades for a given currency pair, you can just ask for the position.

#### Example

Get a list of open positions for account 6531071.

    http://api-sandbox.oanda.com/v1/accounts/6531071/positions

Response:

    {
      "positions" : [
        {
          "side" : "buy",
          "instrument" : "EUR_USD",
          "units" : 1004,
          "avgPrice" : 1.29787
        },
        {
          "side" : "buy",
          "instrument" : "USD_CAD",
          "units" : 298,
          "avgPrice" : 0.99287
        }
      ]
    }

#### Reference
[Reference documentation](https://github.com/oanda/apidocs/blob/master/sections/reference.md#trading-api-overview) for viewing open positions.
-->

### Get transaction history

Transaction history is a record of all activity on an account.  This includes things such as trades opened, orders triggered, and funds deposited.

#### Example
Get the two most recent transactions for account 6531071

    http://api-sandbox.oanda.com/v1/accounts/6531071/transactions?count=2

#####Response:

######Header

~~~Header
Link: <http://api-sandbox.oanda.com/v1/accounts/6531071/transactions?count=2&maxTransId=177810451>; rel="next"
~~~

######Body

~~~Body
{
  "transactions" : [
    {
      "id" : 177810453,
      "accountId" : 6531071,
      "type" : "market",
      "instrument" : "EUR_USD",
      "units" : 1000,
      "side" : "buy",
      "action" : "open",
      "reason" : "user_submitted", 
      "time" : "2013-01-11T15:57:11Z",
      "price" : 1.29732,
      "balance" : 99999.7168,
      "interest" : 0,
      "profitLoss" : 0,
      "upperBound" : 0,
      "lowerBound" : 0,
      "amount" : 1297.32,
      "stopLoss" : 0,
      "takeProfit" : 0,
      "trailingStop" : 0,
      "marginUsed" : 64.866
    },
    {
      "id" : 177810452,
      "accountId" : 6531071,
      "type" : "market",
      "instrument" : "EUR_USD",
      "units" : 1000,
      "side" : "buy",
      "action" : "open",
      "reason" : "user_submitted"
      "time" : "2013-01-11T15:57:11Z",
      "price" : 1.29729,
      "balance" : 99999.7168,
      "interest" : 0,
      "profitLoss" : 0,
      "upperBound" : 0,
      "lowerBound" : 0,
      "amount" : 1297.29,
      "stopLoss" : 0,
      "takeProfit" : 0,
      "trailingStop" : 0,
      "marginUsed" : 64.8645
    }
  ],
}
~~~

#### Reference
[Reference documentation](/docs/v1/transactions) for viewing transaction history.

----

This is just scratching the surface, check out everything you can do in the sidebar.

