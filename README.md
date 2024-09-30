# SimpleFX API Documentation

## Usage

### Obtain API Key

1. Visit [SimpleFX](https://app.simplefx.com/).
2. Go to the menu (hamburger button) and select **Settings**.
   * Alternatively, you can directly visit: [Settings](https://app.simplefx.com/settings).
3. Select **API**.
   * Alternatively, visit: [API Client](https://app.simplefx.com/api-client).
4. Click **Create API Key**.
5. Fill in the API Key creation fields:
   * **Name**: Choose any name you prefer.
   * **Trade Permissions**: Select all available checkboxes to grant your key full permissions.
   * **IP Address Whitelist**: Leave this field empty if you want your key to be accessible from anywhere.
6. Click **Create**.
7. **Copy and save your API key secret**, as you will not be able to view it again.
8. Go back to the **API Key Manager**; your key should appear in either the confirmed or pending list.
9. Confirm your API key using two-factor authentication (2FA) or, if 2FA is not configured, via the email confirmation link.

### Try the Interactive API

Visit the [SimpleFX interactive API](https://simplefx.com/docs/api/swagger) to test API functionality.

#### Obtain ACCESS TOKEN

1. Navigate to **Authentication - POST /api/v3/auth/key**, expand it, and click **Try it out**.
2. You will see the following in the **Edit value** field:
    ```json
    {
      "clientId": "string",
      "clientSecret": "string"
    }
    ```
3. Replace `"string"` with your **API key ID** and **API key secret**:
    ```json
    {
      "clientId": "989c4d4441***********4c1401a9fe8",
      "clientSecret": "bb****d6-a**8-4**e-a**c-4e********97"
    }
    ```
4. Click **Execute**.
   * This will send a request to the SimpleFX server.
5. You can view the response either in the developer tools (press F12) under the **Network** tab or in the response area below.
6. If the API key and secret are valid, the response code will be **200**, and you will receive an ACCESS TOKEN.
7. Copy the token (`data.token`) to your clipboard.

#### Authorize the Interactive API

1. At the top of the interactive API page, click the **Authorize** button.
2. In the **Value** field, enter your ACCESS TOKEN, and click **Authorize**.
3. You are now authorized to use other endpoints in the interactive API.

#### List Your Accounts

1. Navigate to **Accounts - GET /api/v3/accounts**.
2. Expand **Try it out** and click **Execute**.
3. You will receive a response with a list of your trading accounts.

### Understanding the API

The API Key alone cannot be used to authorize requests. You must first obtain an ACCESS TOKEN. Here's a breakdown of the process:

1. **Body**: A JSON format containing your API key ID and secret.
2. **Header**: Ensure the `Content-Type: application/json` header is included so the server understands your request correctly.

Once you obtain the access token, it will be embedded in the browser client and included in further requests. For example, in the `/api/v3/accounts` endpoint, your token will be added as an HTTP header: `Authorization: Bearer <ACCESS_TOKEN>`.

### Create Your Application Using Swagger Docs

Swagger’s interactive API lets you test server interactions, view messages sent and received, and analyze the entire communication process. Learn more about Swagger [here](https://swagger.io/). You can also use [Swagger Codegen](https://swagger.io/tools/swagger-codegen/) to automatically generate API libraries in popular programming languages.

## Quotes and Prices

### **Important Note: The SignalR API is Deprecated**

The SimpleFX SignalR API for streaming quotes has been deprecated. The current implementation uses **SignalR 2.x** (not **SignalR Core**). Future API versions may switch to more modern technologies for real-time data delivery. Please plan accordingly.

Quotes and prices cannot be retrieved via a standard HTTP API because they change within milliseconds. Instead, SimpleFX uses **SignalR** for streaming quotes. You can connect to the quotes stream at this endpoint: [https://webquotes-v3.simplefx.com/signalr/](https://webquotes-v3.simplefx.com/signalr/). The descriptor file is available [here](https://webquotes-v3.simplefx.com/signalr/hubs).

### Obtain Latest Prices

Request:
```json
{
  "H": "quotessubscribehub",
  "M": "getLastPrices",
  "A": [["BTCUSD", "LTCUSD", "ETHUSD", "TLRY.US", "GWPH.US"]],
  "I": 0
}
```
Parameters:
- **hub**: `quotessubscribehub`
- **method**: `getLastPrices`
- **arguments**:
  - 0: List of instruments to request prices for (`["BTCUSD", "LTCUSD", "ETHUSD", "TLRY.US", "GWPH.US"]`).

Response:
```json
{
  "R": {
    "data": [
      { "s": "BTCUSD", "b": 3930.37, "a": 3979.59, "t": 1543223911 },
      { "s": "LTCUSD", "b": 30.3267, "a": 31.8091, "t": 1543223911 },
      { "s": "ETHUSD", "b": 112.38, "a": 115.998, "t": 1543223911 },
      { "s": "TLRY.US", "b": 115.39, "a": 116.1, "t": 1542995999 },
      { "s": "GWPH.US", "b": 123.57, "a": 124.1, "t": 1542995999 }
    ],
    "code": 200,
    "message": "OK",
    "webRequestId": null
  },
  "I": "0"
}
```
Fields:
- **s**: Symbol of the instrument.
- **b**: Bid price.
- **a**: Ask (offer) price.
- **t**: Unix timestamp of the last price update.

### Subscribe to Quotes

Request:
```json
{
  "H": "quotessubscribehub",
  "M": "subscribeList",
  "A": [["BTCUSD", "LTCUSD", "ETHUSD", "TLRY.US", "GWPH.US"]],
  "I": 5
}
```
Parameters:
- **hub**: `quotessubscribehub`
- **method**: `subscribeList`
- **arguments**:
  - 0: List of instruments (`["BTCUSD", "LTCUSD", "ETHUSD", "TLRY.US", "GWPH.US"]`).

Response (Subscription Confirmation):
```json
{"R":{"code":200,"message":"OK","webRequestId":null},"I":"5"}
```

Incoming Quote Events:
```json
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
