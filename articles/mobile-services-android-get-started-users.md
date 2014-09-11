<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />





# Get started with authentication in Mobile Services
<div class="dev-center-tutorial-selector sublanding">   
	<a href="/en-us/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows Store C#">Windows Store C#</a>
    <a href="/en-us/develop/mobile/tutorials/get-started-with-users-js" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/en-us/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/en-us/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a>
    <a href="/en-us/develop/mobile/tutorials/get-started-with-users-android" title="Android" class="current">Android</a>
    <a href="/en-us/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a>
    <a href="/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
	<a href="/en-us/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-android-get-started-users/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>This topic shows you how to authenticate users in Azure Mobile Services from your app. In this tutorial, you add authentication to the quickstart project using an identity provider that is supported by Mobile Services. After being successfully authenticated and authorized by Mobile Services, the user ID value is displayed.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">watch the tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Play Video</span></a><span class="time">10:42</span></div>
</div> 

This tutorial walks you through these basic steps to enable authentication in your app:

1. [Register your app for authentication and configure Mobile Services]
2. [Restrict table permissions to authenticated users]
3. [Add authentication to the app]
4. [Store authentication tokens on the client]
5. [Refresh expired tokens]

This tutorial is based on the Mobile Services quickstart. You must also first complete the tutorial [Get started with Mobile Services]. 

Completing this tutorial requires Eclipse and Android 4.2 or a later version. 

<h2><a name="register"></a><span class="short-header">Register your app</span>Register your app for authentication and configure Mobile Services</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a><span class="short-header">Restrict permissions</span>Restrict permissions to authenticated users</h2>

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

3. In Eclipse, open the project that you created when you completed the tutorial [Get started with Mobile Services]. 

4. From the **Run** menu, then click **Run** to start the app; verify that an unhandled exception with a status code of 401 (Unauthorized) is raised after the app starts. 

	 This happens because the app attempts to access Mobile Services as an unauthenticated user, but the _TodoItem_ table now requires authentication.

Next, you will update the app to authenticate users before requesting resources from the mobile service.

<h2><a name="add-authentication"></a><span class="short-header">Add authentication</span>Add authentication to the app</h2>

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Cache authentication tokens on the client

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Refresh the token cache

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 



## <a name="next-steps"></a>Next steps

In the next tutorial, [Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. 

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-android
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-android

[Azure Management Portal]: https://manage.windowsazure.com/
