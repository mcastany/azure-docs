<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Call a custom API from the client

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-call-custom-api" title="Android" class="current">Android</a><a href="/en-us/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-android-call-custom-api"  title="JavaScript backend" class="current">JavaScript backend</a></div>

This topic shows you how to call a custom API from an Android app. A custom API enables you to define custom endpoints that expose server functionality that does not map to an insert, update, delete, or read operation. By using a custom API, you can have more control over messaging, including reading and setting HTTP message headers and defining a message body format other than JSON.

The custom API created in this topic enables you to send a single POST request that sets the *completed* flag to `true` for all the todo items in your mobile service's table. Without this custom API, the client would have to send individual requests to update the flag for each todo item in the table.

You will add this functionality to the app that you created when you completed either the [Get started with Mobile Services] or the [Get started with data] tutorial. To do this, you will complete the following steps:

1. [Define the custom API]
2. [Update the app to call the custom API]
3. [Test the app] 

This tutorial is based on the Mobile Services quickstart. Before you start this tutorial, you must first complete [Get started with Mobile Services] or [Get started with data]. 

## <a name="define-custom-api"></a>Define the custom API

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]


[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]


## Next steps

Now that you have created a custom API and called it from your Android app, consider finding out more about the following Mobile Services topics:



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
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-android-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-android-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-android-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-android-get-started-push/

[Store server scripts in source control]: /en-us/documentation/articles/mobile-services-store-scripts-source-control
