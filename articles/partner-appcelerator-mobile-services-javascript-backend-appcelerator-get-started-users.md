<properties pageTitle="Get started with authentication (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator team;mahender" />

# Get started with authentication in Mobile Services
<div class="dev-center-tutorial-selector sublanding">
    <a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a>
    <a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
    <a href="/en-us/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a>
    <a href="/en-us/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a>
    <a href="/en-us/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a>
    <a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users" title="Appcelerator" class="current">Appcelerator</a>
</div>

This topic shows you how to authenticate users in Windows Azure Mobile Services from your app. In this tutorial, you add authentication to the quickstart project using an identity provider that is supported by Mobile Services. After being successfully authenticated and authorized by Mobile Services, the user ID value is displayed.

This tutorial walks you through these basic steps to enable authentication in your app:

1.  [Register your app for authentication and configure Mobile Services]
2.  [Restrict table permissions to authenticated users]
3.  [Add authentication to the app]

This tutorial is based on the Mobile Services quickstart. You must also first complete the tutorial [Get started with Mobile Services].

Completing this tutorial requires Appcelerator Titanium Studio 3.2.1 or later and iOS 7.0 and/or later  and Android 4.3 or later.

##<a name="register"></a>Register your app for authentication and configure Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a> Restrict permissions to authenticated users

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

3.	In Appcelerator Titanium Studio, open the project that you created when you completed the tutorial [Get started with Mobile Services].

4.	Press the Run button to build the project and start the app in the iPhone emulator. Verify that an unhandled exception with a status code of 401 (Unauthorized) is raised after the app starts.
    
    This happens because the app attempts to access Mobile Services as an unauthenticated user, but the TodoItem table now requires authentication.

Next, you will update the app to authenticate users before requesting resources from the mobile service.

##<a name="add-authentication"></a>Add authentication to the app

1.	Open the project file index.js and in the table event Lister method, look for `case 2:`

    In your app, you can either prompt the user with available identity providers or automatically provide any one of the identity providers.

2.	To provide all available identity providers, use the following code:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var appName = 'appctest';
        azureMobileServices.setAppName(appName);
        var authenticationClients = ['Google', 'Facebook', 'Twitter', 'Microsoft Account', 'Active Directory', 'Cancel'];
        var dialog = Ti.UI.createOptionDialog({
            options : authenticationClients,
            title : 'Select a client'
        });
        dialog.addEventListener('click', function(evt) {
            if (evt.index == 0 || evt.index == 1 || evt.index == 2 || evt.index == 3 || evt.index == 4) {
                var str = authenticationClients[evt.index];
                str = str.replace(/ /g, '');
                var authorizeClient = str.toLowerCase();
                if (authorizeClient == 'activedirectory') authorizeClient = 'aad';
                azureMobileServices.authorizeClient(authorizeClient, function(result) {
                    if (result == 'true') {
                        Alloy.createController('TableData');
                    }
                });
            } else {
                dialog.hide();
            }
        });
        dialog.show();

3.	To provide particular identity provider, use the following code:

        var azureMobileServiceModule = require( 'com.winwire.azuremobileservices');
        var azureMobileServices = new azureMobileServiceModule.AzureMobileServices();
        var authorizeClient = "Google"; //Replace "Google" with identity provider.
        authorizeClient = authorizeClient.toLowerCase();
        azureMobileServices.authorizeClient(authorizeClient, function(result) {
            if (result == 'true') {
                Alloy.createController('TableData');
            }
        });

>[WACOM.NOTE] If you are using an identity provider other than Google, change the value passed to **authorizeClient** to one of the following: *microsoftaccount*, *facebook*, *twitter* or *windowsazureactivedirectory*.

4.	Press the Run button to build the project, start the app in the iPhone simulator or in Android emulator, and then click on With Login option to log-on with identity provider.

    When you are successfully logged-in, the app should run without errors, and you should be able to query Mobile Services and make updates to data.


<!-- Anchors. -->

[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started