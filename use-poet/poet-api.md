# Po.et API

We are using the [OpenAPI specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.1.md) to document the Frost API:
[https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/)

The purpose of the API is to:
- Facilitate the creation and maintenance of Frost accounts
- Enable Frost account holders to create and view their works on the Po.et Network

#### Tokens

There are four types of tokens generated by Frost (see [src/api/Tokens.ts](./src/api/Tokens.ts)). The two main ones used in the Frost API methods described below are:
- Login token - used to create and maintain the account
- API token - used for creating and viewing works, for both live (mainnet) and test (testnet) environments

There are two other tokens sent by email:
- Verify Account token - used when verifying an email address (see [Verify account](#verify-account) below)
- Forgot Password token - used when resetting a password (see [Password change token](#password-change-token) below)

### [Health check](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/healthCheck)

### Accounts

#### [Create account](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/createAccount)

Returns a Login token.

#### [Verify account](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/verifyAccount)

When the user creates a new account the system automatically sends an email for the purpose of verifying the email address. This email contains a link with an embedded token, which the user only needs to click in order to verify the email address. Alternatively, the token can be passed programatically to the server.

#### [Resend verification email](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/resendVerifyEmail)

Requires a Login token.

#### [Login to account](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/loginAccount)

Authenticate with email address and password.

Returns a Login token.

#### [Get account profile](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/getProfile)

Requires a Login token.

#### [Password reset](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/passwordReset)

When the user forgets or loses the account password, a new one can be requested programatically. An email is sent that contains a link with an embedded token, which the user can click in order to open the Frost website and set a new password.

#### [Password change](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/passwordChange)

Requires a Login token.

#### [Password change token](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/passwordChangeToken)

Requires a Forgot Password token.

### Tokens

#### [Create token](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/createToken)

Requires a Login token.

Returns a "test" or "live" API token.

#### [Get tokens](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/getTokens)

Requires a Login token.

#### [Delete token](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/deleteToken)

Requires a Login token.

### Works

#### [Create work](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/createWork)

Requires an API token.

#### [Get all works](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/getAllWorks)

Requires an API token.

#### [Get work](https://app.swaggerhub.com/apis-docs/po.et/poet-api/0.1.0#/default/getWork)

Requires an API token.