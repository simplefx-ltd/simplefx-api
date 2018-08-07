# simplefx-api
## Usage

### Obtain Api Key
* Visit https://app.simplefx.com/
* From menu (hamburger button), select API and click "create API Key"
* Fill API Key creation fields
  * Name - Be creative, do not put there just "MySimplefxApiKey".
  * Trade Permissions - select all available checkboxes, your key will have all permissions.
  * IP Address whitelist - leave this list empty, your key will have access from whole the world.
  * Click Craete button.
  * !!! Copy and save api key secret !!!, you will not see it again.
  * Check API Key Manager again, your api key shall be there on confirmed or pending list.
  * You need to confirm your key with 2FA authentication, or with email confirmation link if you do not have 2FA configured.

### Try interactive api
Visit simplefx interactive api https://simplefx.com/docs/api/swagger

#### Obtain ACCESS TOKEN
* Navigate to "Authentication / Post /api/v3/auth/key", exapnd it, and exapnd "Try it out" button.
* In Edit value you will see 
```
{
  "clientId": "string",
  "clientSecret": "string"
}
```
* Fill in your api key Id, and api key secret.
* Click execute button.
  * Browser will send your request to simplefx server - you can check it in your browser's developer tools.
* Check the response either in developer tools or in the response area below
* If your api key is verified and secret is correct the response code will 200 and you will get an ACCESS TOKEN.
* Copy your token from data.token to clipboard

#### Authorize interactive api
* On top of the interactive api page there is an "auhtorize" button.
* Click it, fill value with your ACCESS TOKEN, and click "auhtorize".
* You can now use other enpdpoint of the interactive api.

#### List your accounts
* Navigate to Accounts - GET /api/v3/accounts
* Expand "Try it out", and click exeute.
* You will get list of your trading accounts in the response.

### Understanding the api

The Api Key can not be used to authorize requests, you need to obtain ACCESS TOKEN for this.
  Try again obtaining access token and look at the message sent. Important things here are
* body - a json format of your key and secret
* header - "Content-Type: application/json" - so the server understans your body correctly.

Try again authorizating api with access token.
There is no request sent. It justs embeds your ACCESS TOKEN to browser client, so it will sent this token with further requests.

Try again /api/v3/accounts, and look at the message.
Your token is attached as a http header: "Authorization: Bearer <ACCESS_TOKEN>"

## Create your applicaiton based on swagger docs.

Swagger interactive api allows you to interact with the server, showing messages sent and received and allows you to see whole comunication.

Read more about swagger https://swagger.io/

Additionaly it allows to automaticly create api libraries in most popular programing languages:

See: https://swagger.io/tools/swagger-codegen/

# Quotes and prices

Quotes and prices can not be obtained with http API. Prices changes within milliseconds, so you need to connect to quotes stream.
In SimpleFX we use signalr (see https://docs.microsoft.com/en-us/aspnet/signalr ), served from address 
https://webquotes-v3.simplefx.com/signalr/

With descriptor file at 
https://webquotes-v3.simplefx.com/signalr/



