<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />
# Send cross-platform notifications to users with Notification Hubs


In the previous tutorial [Notify users with Notification Hubs], you learned how to push notifications to all devices registered by a specific authenticated user. In that tutorial, multiple requests were required to send a notification to each supported client platform. Notification Hubs supports templates, which let you specify how a specific device wants to receive notifications. This simplifies sending cross-platform notifications. This topic demonstrates how to take advantage of templates to send, in a single request, a platform-agnostic notification that targets all platforms. For more detailed information about templates, see [Azure Notification Hubs Overview][Templates].

<div class="dev-callout"><b>Note</b>
	<p>Notification Hubs allows a device to register multiple templates with the same tag. In this case, an incoming message targeting that tag results in multiple notifications delivered to the device, one for each template. This enables you to display the same message in multiple visual notifications, such as both as a badge and as a toast notification in a Windows Store app.</p>
</div>

Complete the following steps to send cross-platform notifications using templates:

1. In the Solution Explorer in Visual Studio, expand the **Controllers** folder, then open the RegisterController.cs file. 

2. Locate the block of code in the **Post** method that creates a new registration replace the `switch` content with the following code:

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":\"alert\":\"$(message)\"}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":\"msg\":\"$(message)\"}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
	
	This code calls the platform-specific method to create a template registration instead of a native registration. Existing registrations need not be modified because template registrations derive from native registrations.

3. In the **Notifications** controller, replace the **sendNotification** method with the following code:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

	This code sends a notification to all platforms at the same time and without having to specify a native payload. Notification Hubs builds and delivers the correct payload to every device with the provided _tag_ value, as specified in the registered templates.

4. Re-publish your WebApi back-end project.

5. Run the client app again and verify that registration succeeds.

6. (Optional) Deploy the client app to a second device, then run the app. 

	Note that a notification is displayed on each device.

## Next Steps

Now that you have completed this tutorial, find out more about Notification Hubs and templates in these topics:

+ **Use Notification Hubs to send breaking news ([Windows Store C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>Demonstrates another scenario for using templates 

+  **[Azure Notification Hubs Overview][Templates]**<br/>Overview topic has more detailed information on templates.

+  **[Notification Hub How to for Windows Store]**<br/>Includes a template expression language reference.



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /en-us/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Management Portal]: https://manage.windowsazure.com/
[Send cross-platform notifications to users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Breaking news .NET]: /en-us/manage/services/notification-hubs/breaking-news-dotnet
[Breaking news iOS]: /en-us/manage/services/notification-hubs/breaking-news-dotnet
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notify users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx
