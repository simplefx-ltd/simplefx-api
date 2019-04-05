# simplefx-api
## Usage

### Obtain Api Key
* Visit https://app.simplefx.com/
* Go to menu (hamburger button), select API and click Create API Key
* Fill API Key creation fields
  * Name - you can get creative here
  * Trade Permissions - select all available checkboxes, your key will have all permissions
  * IP Address whitelist - leave this list empty, your key will have access from around the world
  * Click Craete
  * **Copy and save api key secret**, you will not see it again
  * Check API Key Manager again, your api key should be there on confirmed or pending list
  * You need to confirm your key with two-factor authentication, or with an email confirmation link if you do not have 2FA configured

### Try interactive api
Visit the SimpleFX interactive API at https://simplefx.com/docs/api/swagger

#### Obtain ACCESS TOKEN
* Go to Authentication - POST /api/v3/auth/key, expand it and click Try it out
* In Edit value you will see 
```
{
  "clientId": "string",
  "clientSecret": "string"
}
```
* Fill in your API key Id, and API key secret
```
{
  "clientId": "989c4d4441***********4c1401a9fe8",
  "clientSecret": "bb****d6-a**8-4**e-a**c-4e********97"
}
```
* Click execute
  * Your browser will send the request to SimpleFX server
* You can check the response either in the developer tools or in the response area below
  * Press F12 for browser developer tools and find "network" tab.
* If your API key is verified and the secret is correct, the response code will be 200 and you will get an ACCESS TOKEN
* Now just copy your token from data.token to clipboard

#### Authorize interactive api
* At the top of the interactive API page there is an Authorize button
* Click it, fill the Value field with your ACCESS TOKEN and then click Authorize
* Now you can now use the other enpdpoint of the interactive API

#### List your accounts
* Navigate to Accounts - GET /api/v3/accounts
* Expand Try it out and click Execute
* As a response you will get a list of your trading accounts

### Understanding the api

The API Key cannot be used to authorize requests; in order to do that, you need to obtain the ACCESS TOKEN. Go through the process of obtaining the access token again and look at the message sent. The important things here are:
* body - a json format of your key and secret
* header "Content-Type: application/json" - so the server understans your body correctly.

Try again authorizing API with the access token. There is no request sent; it just embeds your ACCESS TOKEN in the browser client, so it will be sent with further requests.

Try again /api/v3/accounts, and look at the message. Your token is attached as a http header: "Authorization: Bearer <ACCESS_TOKEN>"

## Create your applicaiton based on swagger docs.

Swagger interactive API allows you to interact with the server, showing messages sent and received, as well as see the whole communication. You can read more about swagger here: https://swagger.io/
Additionally, it allows to automatically create API libraries in the most popular programing languages; see: https://swagger.io/tools/swagger-codegen/

# Quotes and prices

Quotes and prices cannot be obtained with an http API. Prices change within milliseconds, so you need to connect to a quotes stream. In SimpleFX we use signalr (see https://docs.microsoft.com/en-us/aspnet/signalr ), served from the address  https://webquotes-v3.simplefx.com/signalr/ . You can find the descriptor file here: https://webquotes-v3.simplefx.com/signalr/hubs

## Obtain last prices

Request:
```
{
 "H":"quotessubscribehub",
 "M":"getLastPrices",
 "A":[["BTCUSD","LTCUSD","ETHUSD","TLRY.US","GWPH.US"]],
 "I":0
}
```
* hub - quotessubscribehub
* method - getLastPrices
* arguments:
  * 0 - list of instruments - ["BTCUSD","LTCUSD","ETHUSD","TLRY.US","GWPH.US"]

Response:
```
{
  "R": {
    "data": [
      { "s": "BTCUSD", "b": 3930.37, "a": 3979.59, "t": 1543223911 },
      { "s": "LTCUSD", "b": 30.3267, "a": 31.8091, "t": 1543223911 },
      { "s": "ETHUSD", "b": 112.38, "a": 115.998, "t": 1543223911 },
      { "s": "TLRY.US","b": 115.39, "a": 116.1, "t": 1542995999 },
      { "s": "GWPH.US", "b": 123.57, "a": 124.1, "t": 1542995999 }
    ],
    "code": 200,
    "message": "OK",
    "webRequestId": null
  },
  "I": "0"
}
```
* s - symbol of an instrument
* b - bid price
* a - ask (offer) price
* t - unix time stamp of last price

## Subscribe for quotes

Request:
```
{
  "H":"quotessubscribehub",
  "M":"subscribeList",
  "A":[["BTCUSD","LTCUSD","ETHUSD","TLRY.US","GWPH.US"]],
  "I":5
}
```
* hub - quotessubscribehub
* method - subscribeList
* arguments:
  * 0 - list of instruments - ["BTCUSD","LTCUSD","ETHUSD","TLRY.US","GWPH.US"]

Response - subscribe confirmation:
`{"R":{"code":200,"message":"OK","webRequestId":null},"I":"5"}`

Events - incoming quotes
```
{
  "C": "d-7964E528-B,0|_7,1|_8,1",
  "M": [
    {
      "H": "QuotesSubscribeHub",
      "M": "ReceiveQuotes",
      "A": [
        [
          { "s": "LTCUSD", "b": 30.3265, "a": 31.8097, "t": 1543223912 },
          { "s": "BTCUSD", "b": 3930.35, "a": 3979.67, "t": 1543223912 },
          { "s": "ETHUSD", "b": 112.379, "a": 116, "t": 1543223912 }
        ]
      ]
    }
  ]
}
```
