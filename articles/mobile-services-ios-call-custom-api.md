<properties pageTitle="Call a custom API from an iOS app | Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from an iOS app that uses Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="krisragh"  solutions="" writer="krisragh" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Call a custom API from the client 

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-call-custom-api" title="iOS" class="current">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-call-custom-api" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-ios-call-custom-api"  title="JavaScript backend" class="current">JavaScript backend</a></div>

This topic shows you how to call a custom API from an iOS app. A custom API enables you to define custom endpoints that expose server functionality that does not map to an insert, update, delete, or read operation. By using a custom API, you can have more control over messaging, including reading and setting HTTP message headers and defining a message body format other than JSON.

The custom API created in this topic gives you the ability to send a single POST request that sets the completed flag to `true` for all the todo items in the table. Without this custom API, the client would have to send individual requests to update the flag for each todo item in the table.

You will add this functionality to the app that you created when you completed either the [Get started with Mobile Services] or the [Get started with data] tutorial. To do this, you will complete the following steps:

1. [Define the custom API]
2. [Update the app to call the custom API]
3. [Test the app] 

This tutorial is based on the Mobile Services quickstart. Before you start this tutorial, you must first complete [Get started with Mobile Services]. This tutorial requires the [Mobile Services iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533) and [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) and iOS 5.0 or later versions.

## <a name="define-custom-api"></a>Define the custom API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

## Next steps

Now that you have created a custom API and called it from your iOS app, consider finding out more about the following Mobile Services topics:

* [Mobile Services server script reference]
  <br/>Learn more about creating custom APIs.

* [Store server scripts in source control]
  <br/> Learn how to use the source control feature to more easily and securely develop and publish custom API script code.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-ios-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-ios-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-ios-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-ios-get-started-push/
[Store server scripts in source control]: /en-us/documentation/articles/mobile-services-store-scripts-source-control
