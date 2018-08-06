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
* Navigate to "Authentication Post/api/v3/auth/key", exapnd it, and exapnd "Try it out" button.
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
* you will get list of your trading accounts in the response.
