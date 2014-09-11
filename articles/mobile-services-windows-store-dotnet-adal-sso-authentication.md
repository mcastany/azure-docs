<properties linkid="develop-mobile-tutorials-sso-with-adal" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Authenticate your app with Active Directory Authentication Library Single Sign-On

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows Store C#" class="current">Windows Store C#</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

In this tutorial, you add authentication to the quickstart project using the Active Directory Authentication Library. 

To be able to authenticate users, you must register your application with the Azure Active Directory (AAD). This is done in two steps. First, you must register your mobile service and expose permissions on it. Second, you must register your Windows Store app and grant it access to those permissions


>[WACOM.NOTE] This tutorial is intended to help you better understand how Mobile Services enables you to do single sign-on Azure Active Directory authentication for Windows Store apps. If this is your first experience with Mobile Services, complete the tutorial [Get started with Mobile Services].

This tutorial walks you through these basic steps:

1. [Register your mobile service with the Azure Active Directory]
2. [Register your app with the Azure Active Directory] 
3. [Configure the mobile service to require authentication]
4. [Add authentication code to the client app]
5. [Test the client using authentication]

This tutorial requires the following:

* Visual Studio 2013 running on Windows 8.1.
* Completion of the [Get started with Mobile Services] or [Get Started with Data] tutorial.
* Microsoft Azure Mobile Services SDK NuGet package
* Active Directory Authentication Library NuGet package 

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

## <a name="register-app-aad"></a>Register your app with the Azure Active Directory

To register the app with Azure Active Directory, you must associate it to the Windows Store and have a package security identifier (SID) for the app. The package SID gets registered with the native application settings in the Azure Active Directory.


### Associate the app with a new store app name

1. In Visual Studio, right click the client app project and click **Store** and **Associate App with the Store**

    ![][1]

2. Sign into your Dev Center account.

3. Enter the app name you want to reserve for the app and click **Reserve**.

    ![][2]

4. Select the new app name and click **Next**.

5. Click **Associate** to associate the app with the store name.


### Retrieve the package SID for your app.

Now you need to retrieve your package SID which will be configured with the native app settings.

1. Log into your [Windows Dev Center Dashboard] and click **Edit** on the app.

    ![][3]

2. Then click **Services**

    ![][4]

3. Then click **Live Services Site**. 

    ![][5]

4. Copy your package SID from the top of the page.

    ![][6]

### Create the native app registration

1. Navigate to **Active Directory** in the [Azure Management Portal], then click your directory.

    ![][7] 

2. Click the **Applications** tab at the top, then click to **ADD** an app. 

    ![][8]

3. Click **Add an application my organization is developing**.

4. In the Add Application Wizard, enter a **Name** for your application and click the  **Native Client Application** type. Then click to continue.

    ![][9]

5. In the **Redirect URI** box, paste the App package SID you copied earlier then click to complete the native app registration.

    ![][10]

6. Click the **Configure** tab for the native application and copy the **Client ID**. You will need this later.

    ![][11]

7. Scroll the page down to the **permissions to other applications** section and grant full access to the mobile service application that you registered earlier. Then click **Save**

    ![][12]

Your mobile service is now configured in AAD to receive single sign-on logins from your app.



## <a name="require-authentication"></a>Configure the mobile service to require authentication

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Add authentication code to the client app

1. Open your Windows store client app project in Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. In the Solution Explorer window of Visual Studio, open the MainPage.xaml.cs file and add the following using statements.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. Add the following code to the MainPage class which declares the `AuthenticateAsync` method.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. In the code for the `AuthenticateAsync` method above, replace **INSERT-AUTHORITY-HERE** with the name of the tenant in which you provisioned your application, the format should be https://login.windows.net/tenant-name.onmicrosoft.com. This value can be copied out of the Domain tab in your Azure Active Directory in the [Azure Management Portal].

7. In the code for the `AuthenticateAsync` method above, replace **INSERT-RESOURCE-URI-HERE** with the **App ID URI** for your mobile service. If you followed the [How to Register with the Azure Active Directory] topic your App ID URI should be similar to https://todolist.azure-mobile.net/login/aad.

8. In the code for the `AuthenticateAsync` method above, replace **INSERT-CLIENT-ID-HERE** with the client ID you copied from the native client application.

9. In the Solution Explorer window for Visual Studio, open the Package.appxmanifest file in the client project. Click the **Capabilities** tab and enable **Enterprise Application** and **Private Networks (Client & Server)**. Save the file.

    ![][14]

10. In the MainPage.cs file, update the `OnNavigatedTo` event handler to call the `AuthenticateAsync` method as follows.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (user == null)
                await AuthenticateAsync();

            RefreshTodoItems();
        }


## <a name="test-client"></a>Test the client using authentication

1. In Visual Studio,run the client app.
2. You will receive a prompt to login against your Azure Active Directory.  
3. The app authenticates and returns the todo items.

    ![][15]



<!-- Anchors. -->
[Register your mobile service with the Azure Active Directory]: #register-mobile-service-aad
[Register your app with the Azure Active Directory]: #register-app-aad
[Configure the mobile service to require authentication]: #require-authentication
[JavaScript Backend Mobile Service]: #javascript-authentication
[.NET Backend Mobile Service]: #dotnet-authentication
[Add authentication code to the client app]: #add-authentication-code
[Test the client using authentication]: #test-client

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png

<!-- URLs. -->
[How to Register with the Azure Active Directory]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure Management Portal]: https://manage.windowsazure.com/
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Windows Dev Center Dashboard]: http://go.microsoft.com/fwlink/p/?LinkID=266734