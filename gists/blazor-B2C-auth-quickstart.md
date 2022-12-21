https://code-maze.com/azure-active-directory-b2c-with-blazor-webassembly-hosted-apps/

1. new b2c tenant
2. fsblazor.onmicrosoft.com
3. Register Server Application
  
  - BlazorB2C (Server)
> We are not modifying the Redirect URI option because we don’t need it for the server app. Also, we have to grant admin consent, which is checked by default.

    Application (client) ID ... for the SERVER Blazor component
    ```
    <Your GUID Here>
    ```    
    Object ID
    ```
    <Your GUID Here>
    ```
    Directory (tenant) ID
    ```
    <Your GUID Here>
    ```
  - Endpoints
    Azure AD B2C OAuth 2.0 token endpoint (v2)
    https://<yourTenantName>.b2clogin.com/fsblazor.onmicrosoft.com/<policy-name>/oauth2/v2.0/token
  - Branding
    Publisher domain {TENANT DOMAIN}
    ```
    <yourTenantName>.onmicrosoft.com
    ```
  - Expose an API 
    + Generate the Application ID URI (automatic, just click OK)
    https://<yourTenantName>.onmicrosoft.com/<Your GUID> {SERVER API APP ID URI}
    - Add Scopes
      BlazorB2C.Access {DEFAULT SCOPE}
      https://<yourTenantName>.onmicrosoft.com/<Your GUID>/<YourServerAppName>.Access  
      
  
4. Register Client Application
   - BlazorB2C Client
   - Redirect - SPA
     ``` 
     https://localhost:5001/authentication/login-callback
     ```
    Application (client) ID 
    ```
    <Your GUID>
    ```    
    Object ID
    ```
    <Your GUID>
    ```
    Directory (tenant) ID
    ```
    <Your GUID>
    ```
  - Authentication (checkboxes)
>  Now, to confirm the client app registration, we can navigate to the Authentication page:
>  Since we are using the Authorization Code flow (PKCE), we are not going to check any of these options.

  - API Permissions
    - add a permission (MsGraph offline_access and openid should be populated already)
      - 'My APIs'
      - Select name associated with the server app registration created earlier. In this case it is BlazorB2C
        Check the < YourServerAppRegistrationName >.Access box, click [Add Permission]
    - Click Grant admin consent for <yourorgNameHere> (blue check mark)
  
5. New SUSI (App Registrations > Policies > User flows
    - New User Flow - Sign Up and Sign In - Recommended - [Create]
    - 'signupsignin' or whatever
    - Click Email signup radio button
    - Leave the rest at default until #5 (user attributes and token claims)
    - Pick permissions but at least get Given Name and Email Address
    - [Create]
    B2C_1_signupsignin {SIGN UP OR SIGN IN POLICY}
  
    
    
 6. Create Blazor WebAssembly Hosted Application

( Run the following as a single line, or place line breaks your terminal accepts. )
```
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" 
  --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {Server APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"

```
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "https://fsblazor.b2clogin.com/" --api-client-id "f1aef67e-e5f1-4e75-9814-cfb6515dafbb" --app-id-uri "f1aef67e-e5f1-4e75-9814-cfb6515dafbb" --client-id "239d4026-a458-455b-afef-c32ca11eb448" --default-scope "BlazorB2C.Access" --domain "fsblazor.onmicrosoft.com" -ho -o "BlazorB2C" -ssp "B2C_1_signupsignin"
```

# Testing
- restore project
- force app to use localhost:5001
ProjectRoot/Server/Properties/launchSettings.json **and** ProjectRoot/Client/Properties/launchSettings.json
- Change as shown:
```json
  "applicationUrl": "https://localhost:7187;http://localhost:5081",
  "applicationUrl": "https://localhost:5000;http://localhost:5001", *
```
ProjectRoot/Server/appsettings.json, Add CallbackPath after SignUpSignIn (which should be populated):
```json
"SignUpSignInPolicyId": "B2C_1_signupsignin",
"CallbackPath": "/signin-oidc"
```


# Debugging 
first run, error @ msal popup:

```
https://localhost:5001/authentication/login-callback#error=redirect_uri_mismatch&error_description=AADB2C90006%3a+The+redirect+URI+%27https%3a%2f%2flocalhost%3a7187%2fauthentication%2flogin-callback%27+provided+in+the+request+is+not+registered+for+the+client+id+ (...)
```




