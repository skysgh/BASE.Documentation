# About # 


Very Useful links

https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation //if you are struggling to know why your access tokens aren't be validated Very handy
https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect
https://github.com/Azure-Samples/active-directory-dotnet-daemon // Daemon 

Not so Useful but has some code
https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2 // ACTUALLY a terrbile example but included for some code base to use the updated Lib

#Use v1 or v2 endpoint #

not to be confused with code... they are actually 2 different things. 
v2 doesn't actually do what it should, at the moment if a Daemon client is connecting you get No roles, No Scopes or anything
So in order to actually test your application, v1 is basically is all you should probably use.  

V1 is Azure AD appears under App registrations blade(button)
v2 is https://apps.dev.microsoft.com , 
In order to create a v2 app create a unique AD service account with password. Sign in under that user (private/incoginto browse)
Create the app and it should appear under the b2c blade.
NOTE: IT CANNOT BE USED in there, NOR AS A B2C APP EITHER and only the domain account that you created can access it, hence the service account

For more current info
https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-limitations

# Setting up your Application on Azure #

Note App Roles and scopes cannot have the same value :( , sort of annoying. I recommend keeping the same naming convention but adding _role on the end of the value.
e.g. OauthPermisson is core_read so the app role is core_read_role
note: it's worth creating & checking out a v2 instance because the UI may give you hints on what to change in manifest for v1


use the info from both to come to sort of conclusion, they are not one or the other, just an exmaple

## Web Api Application ##

Create as Web app / API
alter the manifest-

Take note of "appId": "cb8b69cd-9020-4860-b043-782d21888cac",
 add the roles for your Dameon Clients under e.g.
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "core_read_role",
      "id": "dd8b69cd-9020-4860-b043-782d21888cdd",
      "isEnabled": true,
      "description": "Core Read for a service",
      "value": "core_read_role"
    }
  ]

add an identifer, The scope identifier   e.g.
"identifierUris": [
    "https://BaseCommonTest.onmicrosoft.com/ADBaseTestWebApi"
  ]

change the oauth permissions, This is for other Web Apps and APIS to use e.g.
"oauth2Permissions": [
    {
      "adminConsentDescription": "core_read",
      "adminConsentDisplayName": "Access ADBaseTestWebApi core_read",
      "id": "0d3fc855-1dab-4c0e-8759-d50554f19195",
      "isEnabled": true,
      "type": "User",
      "userConsentDescription": "core_read",
      "userConsentDisplayName": "Access ADBaseTestWebApi core_read",
      "value": "core_read"
    },
    {
      "adminConsentDescription": "Allow the application to access ADBaseTestWebApi on behalf of the signed-in user.",
      "adminConsentDisplayName": "Access ADBaseTestWebApi",
      "id": "0d3fc855-1dab-4c0e-8759-d50554f19194",
      "isEnabled": true,
      "type": "User",
      "userConsentDescription": "Allow the application to access ADBaseTestWebApi on your behalf.",
      "userConsentDisplayName": "Access ADBaseTestWebApi",
      "value": "user_impersonation"
    }
]

you may need to set the 
"oauth2AllowImplicitFlow": true,

note: it's worth clicking the grant admin access button in the settings -> required permissions, after this is all set up

## Daemon Application ##

Create as Web app / API (its not a native client for some reason)
Create a key under Settings 

alter the manifest-

Not sure if you strictly need to do this , but it's not a bad thing
"identifierUris": [
    "https://BaseCommonTest.onmicrosoft.com/ADDaemon"
  ]

Add required Resources from the Web API(Note keep the one that exists)
Type: Scope = OauthPermissions , Role = Approles
"requiredResourceAccess": [
    {
      "resourceAppId": "cb8b69cd-9020-4860-b043-782d21888cac",
      "resourceAccess": [
        {
          "id": "0d3fc855-1dab-4c0e-8759-d50554f19194",
          "type": "Scope"
        },
        {
          "id": "dd8b69cd-9020-4860-b043-782d21888cdd",
          "type": "Role"
        },
        {
          "id": "0d3fc855-1dab-4c0e-8759-d50554f19195",
          "type": "Scope"
        }
      ]
    },
	{
      "resourceAppId": "00000002-0000-0000-c000-000000000000",
      "resourceAccess": [
        {
          "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
          "type": "Scope"
        }
      ]
    }
  ]


note: it's worth clicking the grant admin access button in the settings -> required permissions, after this is all set up

# General other notes #

add this web.config also if you need to know why things aren't be validated
  <system.diagnostics>
    <switches>
      <add name="Microsoft.Owin" value="Verbose" />
    </switches>
  </system.diagnostics>



# Configuring our application #

## MVC Web Application ##

will default to  $"https://login.microsoftonline.com/{AuthorityTenantName}/v2.0/.well-known/openid-configuration"
    <!--add key="Service-Integration-WebApp-Oidc-ClientAuthorityUri" value="" / --><!-- can override if you need a good reason-->
Our client Id Front end
    <add key="Service-Integration-WebApp-Oidc-ClientId" value="7ab78d6c-***-****-****-***721a0d" />
Secret Generated, (not sure this is 100% needed if you aren't planning on talking other services to but anyways)
    <add key="Service-Integration-WebApp-Oidc-ClientSecret" value="kKN)******IQ;D8`]1" />
Your AD where you get created the apps, (Note it can actually be in a b2c itself!) if using v2 they will appear in the b2c blade if v1 Ad App registrations
    <add key="Service-Integration-WebApp-Oidc-ClientTenant" value="BaseCommonTest.onmicrosoft.com" />
Self explaintory
    <add key="Service-Integration-WebApp-Oidc-ClientRedirectUri" value="https://localhost:44311/" />
    <add key="Service-Integration-WebApp-Oidc-ClientPostLogoutRedirectUri" value="https://localhost:44311/" />

## Web Api Application ##

will default to $"https://login.microsoftonline.com/{this.AuthorityTenantName}/v2.0/.well-known/openid-configuration";
   <!--add key="Service-Integration-Api-Oidc-ClientAuthorityUri" value="" /--><!-- can override if you need a good reason-->
AD instance of the client ID and corresponding URL you set
    <add key="Service-Integration-Api-Oidc-ClientId" value="cb8b69cd-9020-4860-b043-782d21888cac" />
    <add key="Service-Integration-Api-Oidc-ClientApp-IdUri" value="https://BaseCommonTest.onmicrosoft.com/ADBaseTestWebApi" />



#Daemon Services #

see Setting up your Application on Azure
(I haven't figured out the caching thing yet for v2, for application cache, sample code is bad so...)


## v1 Code ##

nuget package - Microsoft.IdentityModel.Clients.ActiveDirectory

these should be all the Azure AD Daemon Client that you created (seperate from your API service)
AzureAdApiIdentifier should be identifierUris specified in your API service

Example Code: 

    private static string aadInstance = "https://login.microsoftonline.com/{0}";
    private static string tenant = "BaseCommonTest.onmicrosoft.com";

    private static string clientId = "965b20c2-d1a1-44ad-8c5d-e426dfcddae0";
    private static string appKey = "*******************";

    static string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);


    var authContext = new AuthenticationContext(authority);
    var clientCredential = new ClientCredential(clientId, appKey);
    token = await authContext.AcquireTokenAsync(AzureAdApiIdentifier, clientCredential);


## v2 Code ##

nuget package - Microsoft.Identity.Client (get the preview version as it stands)

these should be all the Azure AD Daemon Client that you created (seperate from your API service)
the /.default (it can only be /.default) get's all the roles that you granted for the given server, (Do not confused this with the other scope code, it's actually the pointer to the service)
AzureAdApiIdentifier should be identifierUris specified in your API service

Example code :

	private static string aadInstance = "https://login.microsoftonline.com/{0}/v2.0/";
	private static string tenant = "BaseCommonTest.onmicrosoft.com";
	private static string clientId = "965b20c2-d1a1-44ad-8c5d-e426dfcddae0";
	private static string appKey = "*******";
        
	private static string redirectUri = "https://basecommontest.onmicrosoft.com/ADDaemon"; // actually not sure about this but heh

	static string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);

    var clientCredential = new ClientCredential(appKey);
    ConfidentialClientApplication daemonClient = new ConfidentialClientApplication(clientId, authority, redirectUri, clientCredential, null, new TokenCache());
    token = await daemonClient.AcquireTokenForClientAsync(new string[] { AzureAdApiIdentifier + "/.default" });