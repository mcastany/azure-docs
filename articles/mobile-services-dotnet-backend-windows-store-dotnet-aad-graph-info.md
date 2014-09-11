<properties linkid="develop-mobile-tutorials-dotnet-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc" />

# Accessing Azure Active Directory Graph Information

<div class="dev-center-tutorial-selector sublanding">
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title="Windows Store C#" class="current">Windows Store C#</a>
</div>

<div class="dev-center-tutorial-subselector">
	<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET backend" class="current">.NET backend</a> |
	<a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript backend">JavaScript backend</a>
</div>


Like the other identity providers offered with Mobile Services, the Azure Active Directory (AAD) provider also supports a rich [Graph Client Library] which can be used for programmatic access to the directory. In this tutorial you update the ToDoList app to personalize the authenticated user’s app experience based on additional user information you retrieve from the directory using the [Graph Client Library].

>[WACOM.NOTE] The intent of this tutorial is to extend your knowledge of authentication with the Azure Active Directory. It is expected that you have completed the [Get Started with Authentication] tutorial using the Azure Active Directory authentication provider. This tutorial continues to update the TodoItem application used in the [Get Started with Authentication] tutorial. 



This tutorial walks you through the following steps:


1. [Generate an access key for the App registration in AAD] 
2. [Create a GetUserInfo custom API] 
3. [Update the app to use the custom API]
4. [Test the app]

##Prerequisites 

Before you start this tutorial, you must have already completed these Mobile Services tutorials:

+ [Get started with authentication]<br/>Adds a login requirement to the TodoList sample app.

+ [Custom API Tutorial]<br/>Demonstrates how to call a custom API. 



## <a name="generate-key"></a>Generate an access key for the App registration in AAD


During the [Get Started with Authentication] tutorial, you created a registration for the integrated application when you completed the [Register to use an Azure Active Directory Login] step. In this section you generate a key to be used when reading directory information with that integrated application's client ID. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Create a GetUserInfo custom API

In this section, you will create the GetUserInfo custom API that will use the [Graph Client Library] to retrieve additional information about the user from the AAD.

If you've never used custom APIs with Mobile Services, refer to the [Custom API Tutorial] before completing this section.

1. In Visual Studio, right click mobile service .NET backend project and click **Manage NuGet Packages**.
2. In the NuGet Package Manager dialog, enter **ADAL** in the search criteria to find and install the **Active Directory Authentication Library** for your mobile service.
3. In the NuGet Package Manager, also install the **Microsoft Azure Active Directory Graph Client Library** for your mobile service.

4. In Visual Studio, right click the **Controllers** folder for the mobile service project and click **Add** to add a new **Microsoft Azure Mobile Services Custom Controller** named `GetUserInfoController`. The client will call this API to get user information from the Active Directory.

5. In the new GetUserInfoController.cs file, add the following `using` statements.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. In GetUserInfoController.cs, add the following `GetAADToken` method to the class.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    This method uses the app settings you configured on the mobile service in the [Azure Management Portal] to get a token to access the Active Directory.

7. In GetUserInfoController.cs, add the following `GetAADUser` method to the class.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    This method gets the Active Directory object id for the authorized user and then uses the graph client library to get the user's information from the Active Diretory.


8. In GetUserInfoController.cs replace the `Get` method with the following method. This method returns the graph client library's `User` object and requires an authorized user to call the API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. Save your changes and build the the service to verify no syntax errors.
10. Publish the mobile service project to your Azure account. 


## <a name="update-app"></a>Update the app to use GetUserInfo

In this section you will update the `AuthenticateAsync` method you implemented in the [Get Started with Authentication] tutorial to call the custom API and return additional information about the user from the AAD. 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Test the app

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Next steps

In the next tutorial, [Role based access control with the AAD in Mobile Services], you will use role based access control with the Azure Active Directory (AAD) to check group membership before allowing access. 



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]:#next-steps

<!-- Images -->


<!-- URLs. -->
[Get Started with Authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[How to Register with the Azure Active Directory]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure Management Portal]: https://manage.windowsazure.com/
[Custom API Tutorial]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[Store Server Scripts]: /en-us/documentation/articles/mobile-services-store-scripts-source-control/
[Register to use an Azure Active Directory Login]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph Client Library]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/en-us/library/azure/dn151678.aspx
[Role based access control with the AAD in Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
