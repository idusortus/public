# Create .NET 7 Minimal WebAPI With B2C Authentication, test with Postman

## Azure Setup
1. Create Azure B2C Tenant
2. Switch to Tenant
3. App Registrations 
(Reference https://learn.microsoft.com/en-us/azure/active-directory-b2c/tutorial-register-spa)
(Reference https://learn.microsoft.com/en-us/azure/active-directory-b2c/add-web-api-application?tabs=app-reg-ga)
  ### New Registration (API)  
  Name: Z-API  
  Leave 'Accounts in any identity provider....' checked    
  Redirect URI > SPA > https://jwt.ms  && https://oauth.pstmn.io/v1/callback (for postman testing)
  Permissions: Leave Grant admin consent to openid and offline_access permissions checked  
  [Register]  
  - Record Application (client ID)
  3f51546f-f135-41a4-9667-510709bf5f9f
  #### Expose an API
  - Add a scope 
    - (first time, set the Application ID URI to 'api'  )
    - replace random GUID with **api**
    - Should be similar to https://YOURDOMAIN/api/
  - Add a scope
    - weather.read
    - Fill in text boxes with whatever
    - Leave 'state' as enabled
    - [ Add Scope ]
  #### API Permissions
  - Add a permission
    - My APIs
    -  Select your api (Z-API if you're following the example)
    -  Delegated Permissions box should be lit
    -  Check the box by weather.read permission
    -  [ Add permissions ]
  -  Grant admin consent for YOURDOMAIN  
  
  ### New Registration (SPA in this example)  
  + New registration
  + Z-SPA
  + Accounts in any identity provider
  + SPA: https://oauth.pstmn.io/v1/callback
  + Check the box for grant
  + [REGISTER]
  + Add permission
    + MY APis
    + ZAPI
    + weather.read
    + ClientID:  31aadc1e-cdbf-42aa-b7b2-3b0d51cb0594
  
  ### Create a sign-in/sign-up flow
  
  
  ## Visual Studio Code setup
  run
  ```csharp
  dotnet new webapi -minimal -au IndividualB2C -o YOUR-API-PROJECT-NAME
  
  cd YOUR-API-PROJECT-NAME
  code .
  ```
  
  - Update appsettings.json   
  **FROM**
  ```json
  {
/*
The following identity settings need to be configured
before the project can be successfully executed.
For more info see https://aka.ms/dotnet-template-ms-identity-platform
*/
  "AzureAdB2C": {
    "Instance": "https://qualified.domain.name.b2clogin.com/",
    "ClientId": "11111111-1111-1111-11111111111111111",
    "Domain": "qualified.domain.name",
    "Scopes": "access_as_user",
    "SignUpSignInPolicyId": "b2c_1_susi"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
  ```  
  
  **TO**
  ```json
  {
  "AzureAdB2C": {
    "Instance": "https://<YOUR-B2C-DOMAIN-ROOT>.b2clogin.com/",
    "ClientId": "WEB-API-CLIENT_ID",
    "Domain": "<YOUR-B2C-DOMAIN-ROOT>.onmicrosoft.com",
    "Scopes": "weather.read",
    "SignedOutCallbackPath": "/signout/<YOUR-SIGNIN-SIGNUP-USERFLOW>",
    "SignUpSignInPolicyId": "<YOUR-SIGNIN-SIGNUP-USERFLOW>"
    // old scopes: "Scopes": "https://fscwi.onmicrosoft.com/test-api/api.read",
    // "TenantId":"1bfd5960-d2b8-4f77-971a-3fc2f083ef4d",
    // "Authority": "https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/B2C_1_pwa_susi1",
    // "ValidateAuthority": false
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}

  ```
  
  
  ENDPOINTS:
  https://matchhub.b2clogin.com/matchhub.us/<policy-name>/oauth2/v2.0/token
  USERFLOWS:
  B2C_1_boom
  B2C_1_social_ID
  https://ligob2c.b2clogin.com/matchhub.us/v2.0/.well-known/openid-configuration?p=B2C_1_social_ID

Postman:
  PKCE
  https://oauth.pstmn.io/v1/callback
  https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/B2C_1_pwa_susi1/oauth2/v2.0/authorize
  https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/B2C_1_pwa_susi1/oauth2/v2.0/token
  CLIENTID (API)
  3f51546f-f135-41a4-9667-510709bf5f9f
  
   https://ligob2c.b2clogin.com/matchhub.us/B2C_1_social_ID/oauth2/v2.0/authorize
    https://ligob2c.b2clogin.com/matchhub.us/B2C_1_social_ID/oauth2/v2.0/token
  
https://matchhub.us/api/weather.read
  
  
  - 
- server is environment based
- create application database for each client


- MH_UUID_ADMIN
- mh_app_db2 (Norwalk)
- deploy some elements of UUID_V2 db schema to mh_app_db2

	-

B2C Tenant
``````````
fscwi.onmicrosoft.com
B2C_1_pwa_susi1

pwa-spa
```````
64a4fa48-3861-4970-8b41-c12604cbac77

pwa-api (Should be used as clientID in API)
```````
cd670343-38a9-4d4f-a6ca-fb3e097ed9d7
- exposes: 
https://fscwi.onmicrosoft.com/test-api (app id uri)
https://fscwi.onmicrosoft.com/test-api/api.read  (scope access)


Error AADBC90205 = Wrong scope specified in Postman

 IDX20803: Unable to obtain configuration from: '[PII of type 'System.String' is hidden. For more details, see https://aka.ms/IdentityModel/PII.]'.
 = NOT fixed by changing dev certs
 = Added TENANT id... no dice
 + added spa URL: https://localhost/authentication/login-callback
 ... nope
 Found error by adding:
 using Microsoft.IdentityModel.Logging;
 IdentityModelEventSource.ShowPII = true;
 
 ==
 System.InvalidOperationException: IDX20803: Unable to obtain configuration from: 'https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/b2c_1_susi/v2.0/.well-known/openid-configuration'.
 ---> System.IO.IOException: IDX20807: Unable to retrieve document from: 'https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/b2c_1_susi/v2.0/.well-known/openid-configuration'. HttpResponseMessage: 'StatusCode: 404, ReasonPhrase: 'Not Found', Version: 1.1, Content: System.Net.Http.HttpConnectionResponseContent, Headers:
 ==
 Ultimately, I had the wrong b2c policy in the WebAPI. Here's thecompleted thing:
   "AzureAdB2C": {
    "Instance": "https://fscwi.b2clogin.com/",
    "ClientId": "cd670343-38a9-4d4f-a6ca-fb3e097ed9d7",
    "Domain": "fscwi.onmicrosoft.com",
    "Scopes": "https://fscwi.onmicrosoft.com/test-api/api.read",
    "SignedOutCallbackPath": "/signout/B2C_1_pwa_susi1",
    "SignUpSignInPolicyId": "B2C_1_pwa_susi1"
    // "TenantId":"1bfd5960-d2b8-4f77-971a-3fc2f083ef4d",
    // "Authority": "https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/B2C_1_pwa_susi1",
    // "ValidateAuthority": false
 

access_as_user


===
WW-Authenticate: Bearer error="invalid_token", error_description="The audience '3f51546f-f135-41a4-9667-510709bf5f9f' is invalid"
= set audience in config 
=  "Audience":"3f51546f-f135-41a4-9667-510709bf5f9f",
===

https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/B2C_1_pwa_susi1/oauth2/v2.0/auth

https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/B2C_1_pwa_susi1/oauth2/v2.0/token

https://fscwi.b2clogin.com/fscwi.onmicrosoft.com/B2C_1_pwa_susi1/oauth2/v2.0/auth
