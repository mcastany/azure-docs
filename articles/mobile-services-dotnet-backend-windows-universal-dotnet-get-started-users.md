<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="Glenn Gailey" />

# Get started with authentication in Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows C#" class="current">Windows C#</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows JavaScript">Windows JavaScript</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone Silverlight">Windows Phone Silverlight</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/" title=".NET backend" class="current">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/"  title="JavaScript backend">JavaScript backend</a></div>

This topic shows you how to authenticate users in Azure Mobile Services from your universal Windows app. In this tutorial, you add authentication to the quickstart project using an identity provider that is supported by Mobile Services. After being successfully authenticated and authorized by Mobile Services, the user ID value is displayed.

This tutorial walks you through these basic steps to enable authentication in your app:

1. [Register your app for authentication and configure Mobile Services]
2. [Restrict table permissions to authenticated users]
3. [Add authentication to the app]
4. [Store authentication tokens on the client]

This tutorial is based on the Mobile Services quickstart. You must also first complete the tutorial [Get started with Mobile Services]. 

>[WACOM.NOTE]This tutorial shows you how to authenticate users in Windows Store and Windows Phone Store 8.1 apps. For a Windows Phone 8.0 or Windows Phone Silverlight 8.1 app, see this version of [Get started with authentication in Mobile Services](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users).

##<a name="register"></a>Register your app for authentication and configure Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Restrict permissions to authenticated users

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>In Visual Studio, open your client app project and ensure that in App.xaml.cs, the instance of **MobileServiceClient** is configured to use the cloud URL to the mobile service.</li> 
<li><p>Press the F5 key to run this quickstart-based app; verify that an unhandled exception with a status code of 401 (Unauthorized) is raised after the app starts.</p>
   
   	<p>This happens because the app attempts to access Mobile Services as an unauthenticated user, but the <em>TodoItem</em> table now requires authentication.</p></li>
</ol>

Next, you will update the app to authenticate users before requesting resources from the mobile service.

##<a name="add-authentication"></a>Add authentication to the app

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)] 

<!-- Currently not supported
[WACOM.NOTE]If you registered your Windows Store app package information with Mobile Services, you should call the <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> method by supplying a value of <strong>true</strong> for the <em>useSingleSignOn</em> parameter. If you do not do this, your users will still be presented with a login prompt every time that the login method is called.
-->

##<a name="tokens"></a>Store the authorization tokens on the client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[Authorize users with scripts]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript and HTML]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
