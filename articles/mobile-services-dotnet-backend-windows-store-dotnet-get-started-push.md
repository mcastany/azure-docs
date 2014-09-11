<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,ricksal" />


# Get started with push notifications in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title="Windows Store C#" class="current">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android">Android</a>
<!-- <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>


<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push" title=".NET backend" class="current">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/"  title="JavaScript backend">JavaScript backend</a></div>

This topic shows you how to use Azure Mobile Services with a .NET backend to send push notifications to a universal Windows app. In this tutorial you enable push notifications using Azure Notification Hubs to the univeral Windows quickstart project. When complete, your mobile service will send a push notification from the .NET backend using Notification Hubs each time a record is inserted. The notification hub that you create is free with your mobile service, can be managed independent of the mobile service, and can be used by other applications and services.

This tutorial walks you through these basic steps to enable push notifications:

1. [Register your app with WNS and configure Mobile Services](#register)
2. [Update the app to register for notifications](#update-app)
3. [Update the server to send push notifications](#update-server)
4. [Enable push notifications for local testing](#local-testing)
3. [Insert data to receive push notifications](#test)

This tutorial is based on the Mobile Services quickstart. Before you start this tutorial, you must first complete either [Get started with Mobile Services] or [Get started with data] to connect your project to the mobile service. 

>[WACOM.NOTE]This topic supports Windows Phone Store 8.1 apps. To add push notifications to a Windows Phone 8 or Windows Phone Silverlight 8.1 app, see this version of [Get started with push notifications in Mobile Services](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

##<a id="register"></a> Register your app with WNS and configure Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Both your mobile service and your apps are now configured to work with WNS and Notification Hubs. Next, you will update your universal Windows app to register for notifications.

##<a id="update-app"></a> Update the app to register for notifications

Before your app can receive push notifications, you must register a notification channel.

1. In Visual Studio, open the file App.xaml.cs and add the following `using` statements:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Add the following `InitNotificationAsync` method to **App** class to create a push notification channel and register for push notifications: 
	
        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }


    This code retrieves the ChannelURI for the app from WNS, and then registers that ChannelURI for push notifications.
    
3. At the top of the **OnLaunched** event handler in App.xaml.cs, add the following call to the new **InitNotificationsAsync** method:

        InitNotificationsAsync();

	This makes sure that registration is requested every time that the page is loaded. In your app, you may only want to make this registration periodically to ensure that the registration is current. 

4. In Visual Studio, open the Package.appxmanifest file and make sure that **Toast capable** is set to **Yes** on the **Application UI** tab. Save the file.

   	![][1]

   	This makes sure that your app can raise toast notifications. 

##<a id="update-server"></a> Update the server to send push notifications

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Enable push notifications for local testing

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Test push notifications in your app

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Next steps

This tutorial demonstrated the basics of enabling a Windows Store app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

<!--+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->
Learn more about Mobile Services and Notification Hubs in the following topics:

* [Get started with data]
  <br/>Learn more about storing and querying data using mobile services.

* [Get started with authentication]
  <br/>Learn how to authenticate users of your app with different account types using mobile services.

* [What are Notification Hubs?]
  <br/>Learn more about how Notification Hubs works to deliver notifications to your apps across all major client platforms.

* [Mobile Services .NET How-to Conceptual Reference]
  <br/>Learn more about how to use Mobile Services with .NET.

<!-- Anchors. -->

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users

[Send push notifications to authenticated users]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[What are Notification Hubs?]: /en-us/documentation/articles/notification-hubs-overview/
[Send broadcast notifications to subscribers]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Send template-based notifications to subscribers]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/


[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-html-how-to-use-client-library
