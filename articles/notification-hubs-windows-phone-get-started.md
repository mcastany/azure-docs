<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="sethm" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />
# Get started with Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/en-us/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/en-us/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/en-us/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/en-us/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

This topic shows you how to use Azure Notification Hubs to send push notifications to a Windows Phone 8 or Windows Phone 8.1 Silverlight application. If you are targeting Windows Phone 8.1 (non-Silverlight) then refer to the [Windows Universal](/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/) version.
In this tutorial you create a blank Windows Phone 8 app that receives push notifications using the Microsoft Push Notification service (MPNS). When complete, you will be able to broadcast push notifications to all the devices running your app using your notification hub.

> [AZURE.NOTE] Notification Hubs Windows Phone SDK does not support using WNS with Windows Phone 8.1 Silverlight apps. To use WNS (instead of MPNS) with Windows Phone 8.1 Silverlight apps, you have to set up your WNS credentials as shown in [Get Started for Windows Universal](/en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/). Then, you can register from the back-end as shown in the [Notify Users](/en-us/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/) tutorial, or use the [Notification Hubs REST APIs](http://msdn.microsoft.com/en-us/library/dn223264.aspx).

This tutorial walks you through the following steps to enable push notifications:

1. [Create your notification hub]
2. [Connecting your app to the notification hub]
3. [Send notifications from your backend]

The tutorial demonstrates the simple broadcast scenario using notification hubs. Be sure to follow along with the next tutorial to learn how to use notification hubs to address specific users and groups of devices. This tutorial requires the following:

+ [Visual Studio 2012 Express for Windows Phone], or a later version.

Completing this tutorial is a prerequisite for all other notification hubs tutorials for Windows Phone 8 apps. 

<div class="dev-callout"><strong>Note</strong> <p>To complete this tutorial, you must have an active Azure account. If you don't have an account, you can create a free trial account in just a couple of minutes. For details, see <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure Free Trial</a>.</p></div>

##<a name="configure-hub"></a>Create your notification hub

1. Log on to the [Azure Management Portal], click **+NEW** at the bottom of the screen.

2. Click **App Services**, then **Service Bus**, then **Notification Hub**, then **Quick Create**.

   	![][7]

3. Type a name for your notification hub, select your desired region, then click **Create a new Notification Hub**.

   	![][8]

4. Click the namespace you just created (usually ***notification hub name*-ns**), then click the **Configure** tab at the top.

   	![][9]

5. Click the tab **Notification Hubs** at the top, then click on the notification hub you just created.

   	![][10]

6. Click **Connection Information** at the bottom. Take note of the two connection strings.

   	![][12]

7. Click the **Configure** tab, and then click the **Enable unauthenticated push notifications** checkbox in the **Windows Phone notifications settings** section.

   	![][15]

You now have the connection strings required to register your Windows Phone 8 app and send notifications.

<div class="dev-callout"><b>Note</b>
		<p>This tutorial uses MPNS in unauthenticated mode. MPNS unauthenticated mode comes with restrictions on notifications you can send to each channel. Notification Hubs support <a href="http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx">MPNS authenticated mode</a>. <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

##<a name="connecting-app"></a>Connecting your app to the notification hub

1. In Visual Studio, create a new Windows Phone 8 application.

   	![][13]

	In Visual Studio 2013 Update 2 or later, you instead create a Windows Phone Silverlight application.
	
	![][11]	

2. In Visual Studio, right-click the solution, then click **Manage NuGet Packages**. 

	This displays the Manage NuGet Packages dialog box.

3. Search for `WindowsAzure.Messaging.Managed` and click **Install** and accept the terms of use. 

	![][20]

	This downloads, installs, and adds a reference to the Azure Messaging library for Windows using the <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet package</a>. 

4. Open the file App.xaml.cs and add the following `using` statements:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. At the following code at the top of **Application_Launching** method in App.xaml.cs:
	
	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Make sure to insert the name of your hub and the connection string called **DefaultListenSharedAccessSignature** that you obtained in the previous section.
    This code retrieves the ChannelURI for the app from MPNS, and then registers that ChannelURI with your notification hub. It also guarantees that the ChannelURI is registered in your notification hub each time the application is launched.

	>[WACOM.NOTE]This tutorial sends a toast notification to the device. When you send a tile notification, you must instead call the **BindToShellTile** method on the channel. To support both toast and tile notifications, call both **BindToShellTile** and  **BindToShellToast**. 
    
6. In Solution Explorer, expand **Properties**, open the WMAppManifest.xml file, click the **Capabilities** tab and make sure that the **ID___CAP___PUSH_NOTIFICATION** capability is checked.

   	![][14]

   	This ensures that your app can receive push notifications.
	
7. Press the F5 key to run the app.

	A registration message is displayed.

##<a name="send"></a>Send notification from your backend

You can send notifications using Notification Hubs from any back-end using the <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx">REST interface</a>. In this tutorial you send notifications with a .NET console application. For an example of how to send notifications from an Azure Mobile Services backend integrated with Notification Hubs, see **Get started with push notifications in Mobile Services** ([.NET backend](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/) | [JavaScript backend](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)).  For an example of how to send notifications using the REST APIs, see **How to use Notification Hubs from Java/PHP** ([Java](/en-us/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/en-us/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Right-click the solution, select **Add** and **New Project...**, then under **Visual C#** click **Windows** and **Console Application** and click **OK**. 

   	![][6]

	This adds a new Visual C# console application to the solution. You can also do this in a separate solution. 

4. Right-click the , click **Tools**, then click **Library Package Manager**, then click **Package Manager Console**. 

	This displays the Package Manager Console.

6. In the console window, set **Default project** to your new console application project, then in the console window execute the following command:

        Install-Package WindowsAzure.ServiceBus
    
	This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>. 

5. Open the file Program.cs and add the following `using` statement:

        using Microsoft.ServiceBus.Notifications;

6. In the **Program** class, add the following method:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Make sure to replace the "hub name" placeholder with the name of the notification hub that appears in the portal on the **Notification Hubs** tab. Also, replace the connection string placeholder with the connection string called **DefaultFullSharedAccessSignature** that you obtained in the section "Configure your Notification Hub." 

	>[WACOM.NOTE]Make sure that you use the connection string with **Full** access, not **Listen** access. The listen access string does not have permissions to send notifications.

4. Then add the following line in your Main method:

         SendNotificationAsync();
		 Console.ReadLine();

5. With your Windows Phone emulator running and your app closed, set the console application project as the default start up project, then press the F5 key to run the app. 

	You will receive a toast notification. Taping on the toast banner loads the app.

You can find all the possible payloads in the [toast catalog] and [tile catalog] topics on MSDN.

## <a name="next-steps"> </a>Next steps

In this simple example you broadcast notifications to all your Windows Phone 8 devices. In order to target specific users, refer to the tutorial [Use Notification Hubs to push notifications to users]. If you want to segment your users by interest groups, you can read [Use Notification Hubs to send breaking news]. Learn more about how to use Notification Hubs in [Notification Hubs Guidance].

<!-- Anchors. -->
[Create your notification hub]: #configure-hub
[Connecting your app to the notification hub]: #connecting-app
[Send notifications from your backend]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service

[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/en-us/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone 8]: tbd!!!
[MPNS authenticated mode]: http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet
[toast catalog]: http://msdn.microsoft.com/en-us/library/windowsphone/develop/jj662938(v=vs.105).aspx
[tile catalog]: http://msdn.microsoft.com/en-us/library/windowsphone/develop/hh202948(v=vs.105).aspx
