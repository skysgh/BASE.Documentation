# About # 

How to Create a b2c Tenant https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-tenant

This is a guide on how to set up your app to connect to Azure B2C,
It's a general work of thought, I don't know how much of this is useful, and there are guides online too 

Very useful links 
https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi
https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation //if you are struggling to know why your access tokens aren't be validated Very handy



# Creating an B2C Application On Azure #
(Going to have to do this for each environment.. Redirect URIs must all belong to the same domain)


This assumes you already have set up a bunch of Policies
Add application Web App / Web API
Under Properties add the REPLY URL & Give An API ID pref similiar to name
Generate and store key if necessary,
Publish Some scopes that you are going to secure your API's behind, more revelant for API auth tokens
Go To API access import other apps scopes (and I guess expose your own to yourself?)



not really too much to say

# General other notes #

Currently there are actually 3 issuers for microsoft, Yup...............................................OH AND YES THE / AT THE END MATTERS!
https://sts.windows.net/<tenant>/
https://login.microsoftonline.com/<tenant>/
https://login.microsoftonline.com/<tenant>/v2.0/


Currently app will validate Issuers to the above. 
Also app will validate the Audiences, hence why the dual Config (below) One for b2c and one for AD, 

add this web.config also if you need to know why things aren't be validated
  <system.diagnostics>
    <switches>
      <add name="Microsoft.Owin" value="Verbose" />
    </switches>
  </system.diagnostics>



# Configuring our application #


## MVC Web Application ##

will default to  $"https://login.microsoftonline.com/tfp/{this.AuthorityTenantName}/{this.DefaultPolicyId}/v2.0/.well-known/openid-configuration"
    <!--add key="Service-Integration-WebApp-Oidc-ClientAuthorityUri" value="" / --><!-- can override if you need a good reason-->
Our client Id Front end
    <add key="Service-Integration-WebApp-Oidc-ClientId" value="7ab78d6c-***-****-****-***721a0d" />
Secret Generated, (not sure this is 100% needed if you aren't planning on talking other services to but anyways)
    <add key="Service-Integration-WebApp-Oidc-ClientSecret" value="kKN)******IQ;D8`]1" />
The b2c Tenant you created
    <add key="Service-Integration-WebApp-Oidc-ClientTenant" value="BaseCommonTest.onmicrosoft.com" />
Self explaintory
    <add key="Service-Integration-WebApp-Oidc-ClientRedirectUri" value="https://localhost:44311/" />
    <add key="Service-Integration-WebApp-Oidc-ClientPostLogoutRedirectUri" value="https://localhost:44311/" />

    <add key="Service-Integration-WebApp-Oidc-Policies-DefaultPolicyId" value="B2C_1_B2C_Default_Policy" />
    <add key="Service-Integration-WebApp-Oidc-Policies-SignUpSignInPolicyId" value="B2C_1_B2C_Default_Policy" />
    <add key="Service-Integration-WebApp-Oidc-Policies-UserProfilePolicyId" value="B2C_1_B2C_Default_Policy" />
    <add key="Service-Integration-WebApp-Oidc-Policies-EditProfilePolicyId" value="B2C_1_B2C_Default_Edit_Policy" />
    <add key="Service-Integration-WebApp-Oidc-Policies-ResetPasswordPolicyId" value="B2C_1_B2C_Default_Reset_Policy" />
    <!--add key="Service-Integration-WebApp-Oidc-policies-SignUpnPolicyId" value="" /--><!-- Technically deprecated but can set it -->
    <!--add key="Service-Integration-WebApp-Oidc-policies-SignInPolicyId" value="" /--><!-- Technically deprecated but can set it -->

## Web Api Application ##

will default to $"https://login.microsoftonline.com/{this.AuthorityTenantName}/v2.0/.well-known/openid-configuration";
   <!--add key="Service-Integration-Api-Oidc-ClientAuthorityUri" value="" /--><!-- can override if you need a good reason-->
AD instance of the client ID and corresponding URL you set
    <add key="Service-Integration-Api-Oidc-ClientId" value="cb8b69cd-9020-4860-b043-782d21888cac" />
    <add key="Service-Integration-Api-Oidc-ClientApp-IdUri" value="https://BaseCommonTest.onmicrosoft.com/ADBaseTestWebApi" />
The b2c Tenant you created
    <add key="Service-Integration-Api-Oidc-ClientTenant" value="BaseCommonTest.onmicrosoft.com" />
API instance of the client ID and corresponding URL you set in b2c, if you are creting a webapp and API then these SHOULD DIFFER
    <add key="Service-Integration-Api-Oidc-ClientIdB2C" value="36f35e49-1b75-4942-ae4b-53b011abdd32" /> <!-- if app is b2c configured/required set -->
    <add key="Service-Integration-Api-Oidc-ClientApp-IdUriB2C" value="https://BaseCommonTest.onmicrosoft.com/BaseTestWebApi" /> <!-- if app is b2c configured/required  set-->
The Policy ID That the web App will be signing in as, same as your front end
    <add key="Service-Integration-Api-Oidc-Policies-PolicyIdB2C" value="B2C_1_B2C_Default_Policy" /> <!-- if app is b2c configured/required  set-->

# Limitations # 

if something doesn't seem to work it's probably likely that microsoft hasn't actually implemented it yet
https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-limitations, this seems to include B2C 

but wait there is more... 

B2C does not seem to allow on-behalf-flow , instead they you use some crazy IUser, which I HAVE NO CLUE how the idenitifer is actually generated for
some crazy magic which only leads me to believe a wizzard actually did. When using Cookie Authentication there seems to be a to have an ability to do do this, although
App.Core.Infrastructure.IDA.Oidc.MSALSessionCache should be rectified not to use HttpContext and use either Redis or Db. (or both)
https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization (supposedly helps)

B2C also cannot talk to any of your Azure AD (v1 & v2) to supply a list of Scopes to get, Only other B2C apps when using the above. So if you are creating a backend 
service past your front end service, you should register it as both an Azure under App Registrations and Azure B2C App Regigistrations.
(see not sure if you can hack it to talk to your backend)

Currently because there are no Roles or Groups for B2C, Unless you want open APIs with no scope or Roles then Daemon (no user interaction) testing becomes nigh impossible
another reason to register any common WebApi as both B2C app and an Azure AD app. Testing I know what a pain but still..

# Gotchas #

If you a request a scope(s) that your application doesn't actually have access too, then you will not get back an Access_Token to forward on to other apps, Yup.. it's null
you still get an IdToken though....
as long as there is one valid scope, pretty sure it works though. 


# Not sure... but maybe #

Possibly use an authtoken and use Your other AD cred to actually do the on-behalf of flow?????????????????????????????????
Seems like a hack that may work, probably won't 




# Setting up an IDP #

One other than the the Azure AD, microsoft Docs are probably the first port of call. 

## Connecting to Google ##

https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-setup-goog-app

ensure the the auth response is {tenant} is lowercase can be a little bit of pitfall

https://aadguide.azurewebsites.net/aadb2c/aadb2cpolicies/

