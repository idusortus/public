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

  Permissions: Leave Grant admin consent to openid and offline_access permissions checked  
  [Register]  
  - Record Application (client ID)
  e366e1bb-b4ba-49a1-86d6-eac06f60cbbb
  #### Expose an API
  - Add a scope 
    - (first time, set the Application ID URI to 'api'  )
    - replace random GUID with **api**
    - Should be similar to https://YOURDOMAIN/api/ 
  - Add a scope
    - access_as_user  **can change this or add scopes later, this is what the default dotnet webapi sets up initially**
    - longform: https://spinachpizza.onmicrosoft.com/api/access_as_user
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
  > Redirect URI > SPA > https://jwt.ms  && https://localhost/authentication/login-callback && https://oauth.pstmn.io/v1/callback (for postman testing)
  + Leave Boxes Unchecked for this example
  + [REGISTER]
  + API Permissions > Add permission
    + MY APis
    + ZAPI
    + access_as_user
    + - Grant admin consent
    + ClientID:  0c275dcc-b12c-45be-a8a9-4bba2cd959ca
  
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
  

### Postman Setup

New Collection
- Authorization type: OAuth 2.0
- Grant Type: Authorization Code (With PKCE)
- Callback URL: https://oauth.pstmn.io/v1/callback
- Auth URL:
- Access Token URL: https://spinachpizza.b2clogin.com/spinachpizza.onmicrosoft.com/b2c_1_susi/oauth2/v2.0/token
- Client ID: 
- Scope: access_as_user
